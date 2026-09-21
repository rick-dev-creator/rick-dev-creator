# Arquitectura — backend

> Define **la forma** que sigue cada módulo, no su implementación. Sin código.
> Frontend en [04-frontend.md](./04-frontend.md).

---

## 1. Decisiones de forma

| | Decisión |
|---|---|
| **Despliegue** | Modular monolith. Un solo proceso, un solo desplegable |
| **Interior de cada módulo** | Clean Architecture, dependencias hacia dentro |
| **Persistencia** | Un PostgreSQL, **un schema por módulo** |
| **Comunicación entre módulos** | Eventos de integración in-process + contratos publicados |
| **Orleans** | **Solo en Desk** (§5) |
| **Orquestación local** | .NET Aspire |

La razón de fondo del monolito modular aquí: **cinco módulos con perfiles de carga
ridículamente distintos** (Calls procesa 20 proyecciones por semana; Desk mantiene
websockets vivos) pero **un solo operador y un solo equipo**. Distribuir esto en servicios
sería pagar el coste operativo de los microservicios sin ninguno de sus beneficios.

Lo que sí importa es que **las costuras estén bien puestas**, para que el día que Desk
necesite escalar distinto sea una mudanza y no una reescritura.

---

## 2. Anatomía de un módulo

Todos los módulos tienen la misma forma. Ejemplo con Calls:

```
Modules/Calls/
├── Calls.Domain           entidades, value objects, reglas. CERO dependencias
├── Calls.Application      casos de uso, puertos (interfaces), handlers
├── Calls.Infrastructure   EF Core, adaptadores externos. Implementa los puertos
├── Calls.Contracts        ← lo ÚNICO que otros módulos pueden ver
└── Calls.Api              endpoints, registrados por el host
```

**La regla de dependencia** apunta siempre hacia dentro: `Api → Application → Domain`, e
`Infrastructure → Application` (implementa sus puertos). El dominio no conoce EF Core, ni
HTTP, ni Orleans, ni a ningún otro módulo.

### Qué va en Domain — y por qué importa aquí más que de costumbre

En Calls, **el motor de resolución vive entero en Domain como función pura**:

```
Resolve(Projection, CandleSeries) → Resolution
```

Sin I/O, sin base de datos, sin reloj del sistema. Recibe la proyección y las velas,
devuelve el veredicto.

Esto no es purismo. Las reglas de resolución son el 80% del riesgo del producto (doc 10 §5),
y una función pura convierte el ejercicio recomendado —**resolver a mano 10 proyecciones
reales**— directamente en la suite de tests. Cada caso raro que descubras (el gap, el empate
de la regla conservadora, el split) se vuelve un test que no se puede romper después.

Si esa lógica se mezcla con acceso a datos, ese ejercicio deja de ser ejecutable y pierdes
la única red de seguridad que tiene el producto.

---

## 3. Reglas de independencia entre módulos

Cinco reglas. Si se cumplen, es un monolito modular; si se rompe una, es un monolito a secas
con carpetas bonitas.

1. **Un módulo nunca lee las tablas de otro.** Schema propio, migraciones propias, **sin
   foreign keys entre schemas**. Las referencias cruzadas son IDs sueltos, validados por
   contrato, no por la base de datos.
2. **Un módulo solo ve el proyecto `*.Contracts` de otro.** Nunca su Domain, Application ni
   Infrastructure. Esto se verifica con un test de arquitectura, no con buena voluntad.
3. **Toda comunicación es por contrato**: un evento de integración o una interfaz publicada
   en Contracts.
4. **Sin ciclos.** Si aparece uno, se invierte con un evento en vez de con una referencia.
5. **El shared kernel se mantiene diminuto.** Algo entra solo si lo necesitan tres módulos o
   más **y** no tiene comportamiento que pertenezca a ninguno: `Money`, `Activity`,
   `Timeframe`. Nada más. Un shared kernel que crece se convierte en un módulo-dios y anula
   toda la independencia.

---

## 4. Los módulos y cómo se relacionan

Hay un módulo que no estaba en el mapa de producto porque es puramente técnico:
**MarketData**. Calls y Journal lo necesitan, con capacidades distintas (doc 90 §6).

```
MarketData ──► Calls ──► Journal
                 ▲          ▲
                 └── Desk ──┘

Club ── no depende de nadie; solo publica y escucha eventos
```

### Eventos de integración

| Evento | Origen | Consumidores |
|---|---|---|
| `CandlesIngested` | MarketData | Calls (dispara resolución) |
| `ProjectionPublished` | Calls | Club (aviso a Discord) |
| `ProjectionResolved` | Calls | Club (aviso), Journal (cierra el cruce) |
| `PositionOpened` / `PositionClosed` | Desk | Journal (registro automático), Club (aviso a miembros) |
| `EntitlementChanged` | Club | Desk (concede o revoca acceso) |

Nótese la inversión de la regla 4: Desk necesita saber si un usuario tiene acceso, pero
**Club no depende de Desk**. Club publica `EntitlementChanged` y Desk mantiene su propia
copia local. Sin ciclo.

### MarketData: abstracción por capacidad, no por proveedor

Dos puertos distintos, porque son dos necesidades con costes distintos (doc 90 §1):

- **`IPriceSnapshot`** — precio actual y cierre diario. Lo usa Journal. Gratis en las tres
  clases de activo.
- **`ICandleHistory`** — OHLC intradía hasta 5 minutos. Lo usa el motor de Calls. Es lo
  único que cuesta dinero.

Separarlos permite que Journal nunca arrastre el coste de Calls, y que cambiar de proveedor
para una capacidad no toque la otra. Cada vela ingerida se guarda: el histórico propio es
lo que permite reevaluar el récord completo con los mismos datos originales.

---

## 5. Orleans: solo en Desk

Evaluado módulo por módulo, sin concesiones al stack preferido:

| Módulo | Orleans | Por qué |
|---|---|---|
| Calls | **No** | 20 proyecciones/semana, resolución por lotes tras ingerir velas. Un `BackgroundService` y Postgres sobran |
| Journal | **No** | CRUD sobre datos privados |
| MarketData | **No** | Ingesta programada y caché |
| Club | **No** | Reconciliación periódica. Un worker programado |
| **Desk** | **Sí** | El único con estado vivo, concurrencia por entidad y conexiones persistentes |

### Por qué Desk sí

No por volumen: por **concurrencia sobre entidades con dinero real**.

- `ExchangeConnectionGrain` **(usuario × exchange)** — dueño del websocket, mantiene el
  estado vivo de la sesión y **serializa los comandos** hacia ese exchange.
- `LadderGrain` **(por escalera)** — máquina de estados de reconciliación: llenados
  parciales, cancelaciones a medias, reintentos.
- `PositionGrain` **(por posición)** — estado agregado y su bucle de reconciliación.

Lo que da Orleans aquí y no da un hosted service:

- **Una sola hebra por entidad, sin locks explícitos.** Elimina la clase de bug más
  peligrosa del proyecto: dos operaciones concurrentes sobre la misma escalera. Doc 35 §2.2
  explica que mover o cancelar una escalera son N llamadas sin atomicidad; un grain garantiza
  que no se solapan dos intentos.
- **Estado caliente en memoria** con persistencia, sin recargar desde Postgres en cada tick.
- **Timers por grain** para el bucle de reconciliación contra el estado real del exchange.
- **Activación bajo demanda**: solo viven los grains de cuentas y escaleras activas.

### La disciplina que no se puede romper

**Los grains son adaptadores de infraestructura, no entidades de dominio.**

`LadderGrain` **hospeda** un `Ladder` del dominio y serializa el acceso a él. El `Ladder`
sigue siendo una clase de `Desk.Domain`, sin referencias a Orleans, testeable sin silo.

Es el error clásico al juntar Orleans con Clean Architecture: convertir los grains en el
modelo de dominio. Cuando pasa, el dominio se vuelve intestable, se acopla al framework de
por vida, y la lógica de negocio queda enterrada bajo detalles de activación y persistencia
de grains.

---

## 6. Persistencia

- **Un PostgreSQL**, un schema por módulo: `calls`, `journal`, `desk`, `club`, `marketdata`.
- **Migraciones por módulo**, aplicadas de forma independiente.
- **Sin foreign keys entre schemas.** Una posición de Journal referencia una proyección de
  Calls por ID, y la integridad la garantiza el contrato, no la base de datos.
- **Las velas son la única tabla con volumen real.** Es serie temporal: particionado por
  tiempo y política de retención por timeframe (las de 5 minutos no hacen falta para
  siempre; las diarias sí).
- **Inmutabilidad de Calls** (doc 10, P2): append-only a nivel de aplicación —
  versiones nuevas en vez de `UPDATE` sobre los campos de calificación. El volumen es tan
  bajo que no hace falta event sourcing completo para conseguirlo.

---

## 7. Despliegue y evolución

Un proceso. Aspire orquesta Postgres, el host web y el silo de Orleans co-hospedado en el
mismo proceso.

**La costura que conviene cuidar desde el principio** es Desk: sus websockets son conexiones
largas con un perfil de escalado distinto al resto. Si algún día hay que sacarlo a su propio
proceso, las reglas de §3 hacen que sea mover un proyecto y cambiar el transporte del bus de
eventos —no reescribir.

Eso es exactamente lo que se compra con el monolito modular: **la opción de separar, sin
pagar hoy por ella.**

---

## 8. Cómo se verifica que esto se cumple

Tres controles automáticos, que valen más que cualquier documento:

1. **Test de arquitectura** que falla si un módulo referencia algo de otro que no sea
   `*.Contracts`.
2. **Test de dependencia de capas**: `Domain` sin referencias salientes; `Application` sin
   EF Core, sin HTTP y sin Orleans.
3. **Test del motor de resolución** con los casos reales del ejercicio de las 10
   proyecciones (§2), incluidos los casos raros: gap de apertura, empate de la regla
   conservadora, split.

Sin el primero, la modularidad se erosiona en semanas. Es la diferencia entre un monolito
modular y un monolito con carpetas.
