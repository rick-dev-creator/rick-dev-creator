# Journal — Módulo 2

> Registro privado de tus posiciones. Auto-diagnóstico, no portfolio tracker.

> Documento conceptual. Depende de [10-calls.md](./10-calls.md).
> Fuentes de precios en [90-fuentes-de-datos.md](./90-fuentes-de-datos.md).

---

## 1. Qué es y qué no es

**Es** un registro privado de tus posiciones, capturado a mano, cuyo único propósito es
responder preguntas que ninguna app de portfolio puede responder porque no conocen tus
proyecciones.

**No es** un portfolio tracker. No compite con CoinStats, Delta ni Rotki. No calcula
impuestos, no reconcilia transferencias entre cuentas, no persigue airdrops.

### La decisión que define el módulo

Se evaluó construir un portfolio público (prueba de *skin in the game* ante la audiencia)
y se descartó: expone patrimonio de forma permanente, agrava el perfil regulatorio de
publicar proyecciones, y exige verificación on-chain o por API para valer algo.

Al ser **privado**, tres cosas se simplifican de golpe:

| | Consecuencia |
|---|---|
| **Riesgo patrimonial** | Desaparece. No se publica nada. |
| **Riesgo regulatorio** | Desaparece. No hay divulgación de posiciones junto a las proyecciones. |
| **Verificabilidad** | Deja de ser requisito. La captura manual basta: no tienes incentivo para mentirte a ti mismo. |

Esto es lo que hace viable el módulo. Un portfolio público manual sería palabra contra
palabra; uno privado manual es simplemente tu bitácora.

---

## 2. Por qué construirlo en vez de usar CoinStats

Porque la pregunta que te interesa no es "¿cuánto gané?". Es **"¿hice lo que dije?"**.

CoinStats sabe tu P&L. No sabe que el 12 de marzo proyectaste ETH alcista con invalidación
en 2,800, y por lo tanto no puede decirte que el precio rompió 2,800 el día 19 y tú
seguiste dentro tres semanas más.

El valor del módulo 2 está **entero en el cruce con el módulo 1**. El journal es solo la
infraestructura mínima para calcular ese cruce.

---

## 3. Las preguntas que el sistema debe responder

Estas preguntas son la especificación. Si una funcionalidad no contribuye a responderlas,
no entra.

### Coherencia entre lo que dices y lo que haces

- ¿En qué % de mis proyecciones alcistas **realmente tomé posición**?
- ¿Tomo posición en proyecciones de confianza alta más que en las de confianza baja?
- ¿Cuántas posiciones abrí **sin** haber hecho una proyección previa? (operar por impulso)

### Disciplina

- **¿Respeté mi propia invalidación?** Cuando el precio rompió el nivel que yo mismo
  definí, ¿salí o me quedé aguantando?
- ¿Salí antes de tiempo en proyecciones que acabaron siendo correctas? (dejar dinero en la
  mesa)
- ¿Entré al precio que dije en el video, o perseguí el precio después?

### Ejecución

- ¿Mi R real coincide con el R que proyecté? (§7.2 del módulo 1)
- ¿Mis proyecciones acertadas se traducen en posiciones ganadoras, o acierto el análisis y
  fallo la ejecución?

Esa última distinción es la más valiosa del módulo: separa **"mi TA es malo"** de **"mi TA
es bueno pero mi ejecución es mala"**. Son dos problemas distintos con soluciones distintas,
y hoy no tienes forma de saber cuál tienes.

---

## 4. Modelo de dominio

### 4.1 Entidad principal

```
Position
├── Asset                  símbolo + clase (reutiliza el Asset del módulo 1)
├── Activity               Trading | Investing (debe coincidir con la proyección enlazada)
├── Direction              Long | Short
├── OpenedAt               fecha de entrada
├── EntryPrice             precio de entrada
├── Size                   tamaño (§4.2)
├── ClosedAt               fecha de salida (null si sigue abierta)
├── ExitPrice              precio de salida (null si sigue abierta)
├── ProjectionRef          → Projection del módulo 1 (nullable, §4.3)
├── ExitReason             por qué saliste (§4.4)
└── Notes                  texto libre
```

Eso es todo. Deliberadamente no hay: fees, tax lots, transferencias, staking, airdrops,
dividendos, cuentas ni exchanges. Nada de eso ayuda a responder §3.

### 4.2 Tamaño: porcentaje, no monto

El tamaño se registra como **% del portfolio en el momento de abrir**, no en dinero.

Tres razones:
1. Responde la pregunta que importa ("¿le di peso a mi convicción?") tan bien como el monto.
2. Elimina la necesidad de rastrear depósitos, retiros y el valor total de la cartera —
   que es exactamente la complejidad que hunde estos proyectos.
3. Si algún día publicas métricas agregadas (§8), los porcentajes ya son publicables;
   los montos nunca lo serían.

### 4.3 Posiciones sin proyección, y proyecciones sin posición

Ambos casos son válidos y **ambos son datos, no huecos**:

- **Posición sin proyección** = operaste sin tesis pública. El conteo de estas es una
  métrica de disciplina por sí solo.
- **Proyección sin posición** = analizaste pero no actuaste. Cruzado con el resultado de la
  proyección, dice si dejas pasar tus buenas ideas.

El sistema sugiere el enlace automáticamente (misma clase de activo + símbolo + ventana
temporal), pero **nunca lo crea solo**. Confirmarlo es un clic.

### 4.4 Razón de salida

Campo cerrado, no texto libre. Es lo que convierte el Journal en una herramienta de
diagnóstico:

| Razón | Qué revela |
|---|---|
| `TargetAlcanzado` | Ejecución según plan |
| `InvalidaciónRespetada` | Disciplina |
| `SalidaAnticipada` | Impaciencia o miedo |
| `SalidaTardía` | Te quedaste después de romper tu propio nivel |
| `CambioDeTesis` | Razón legítima, pero vigilar su frecuencia |
| `Liquidez` | Necesitabas el capital, ajeno al análisis |

### 4.5 Ciclo de vida

```
Abierta ──────► Cerrada
   │               │
   └── editable ───┘  siempre editable: es privado, sin garantía de inmutabilidad
```

**Diferencia deliberada con el módulo 1.** Las proyecciones son inmutables porque su valor
depende de que nadie pueda alterarlas. Las posiciones son privadas: la inmutabilidad solo
te estorbaría al corregir un dedazo. No se hereda el principio P2.

---

## 5. Valuación

Necesidad de datos: **precio actual y cierre diario**. Nada más.

Esto es mucho más barato que el módulo 1, que requiere OHLC intradía hasta 5 minutos para
la regla de desempate (§5.4 del módulo 1). El módulo 2 **no añade dependencias nuevas**:
reutiliza la capa de datos de mercado del módulo 1 y consume su camino más barato.

- **Sin precios en vivo.** Es una bitácora de auto-auditoría, no un dashboard de trading.
  Refresco diario más botón de actualizar a demanda.
- **Un símbolo se consulta mientras tenga una posición abierta**, o una proyección activa
  del módulo 1.
- **Moneda de reporte única** (USD o MXN, a decidir). Las posiciones en otra divisa se
  convierten con tipo de cambio diario.

Fuentes concretas y sus límites: ver [90-fuentes-de-datos.md](./90-fuentes-de-datos.md).

---

## 6. Superficies

Todo detrás del mismo login de admin del módulo 1. **Nada de esto es público.**

- **Captura rápida** — activo, dirección, precio de entrada, tamaño en %, y sugerencia de
  proyección para enlazar. Mismo presupuesto que el módulo 1: menos de 2 minutos.
- **Posiciones abiertas** — con su proyección enlazada y una alerta visible cuando el
  precio ha roto la invalidación de esa proyección y la posición sigue abierta. Esa alerta
  es, en sí misma, la mitad del valor del módulo.
- **Bitácora cerrada** — historial filtrable por activo, razón de salida y resultado.
- **Informe de coherencia** — las métricas de §3, que es la pantalla que justifica todo.

---

## 7. Fuera de alcance

- Precios en tiempo real o gráficas de velas.
- Fees, slippage, tax lots, cost basis contable, reportes fiscales.
- Transferencias entre cuentas, staking, airdrops, forks, dividendos.
- Conexión a exchanges por API key o a wallets on-chain.
- Multi-usuario o cualquier vista pública.
- Alertas push (el módulo 1 ya tiene el bot de Discord; aquí no aplica, es privado).

---

## 8. Una previsión barata ahora, cara después

El journal es privado y así se queda. Pero las **métricas agregadas** de §3 sí son
publicables algún día sin revelar una sola posición:

> "Respeté mi nivel de invalidación en el 78% de las operaciones."

Eso refuerza el récord público del módulo 1 y no expone nada: ni montos, ni activos, ni
patrimonio. Por eso el tamaño va en % (§4.2) y por eso las métricas se calculan como
agregados derivados, separados del Journal crudo.

**No se construye nada de esto en el módulo 2.** Solo se evita cerrarse la puerta, que hoy
cuesta cero y después cuesta una migración.

---

## 9. Decisiones abiertas

Las decisiones abiertas de este módulo (D7–D10) viven en el registro único:
**[02-decisiones.md](./02-decisiones.md)**. No se duplican aquí para que no se
desincronicen.

---

## 10. Relación con el módulo 1

| | Módulo 1 — Proyecciones | Módulo 2 — Journal |
|---|---|---|
| **Visibilidad** | Público | Privado |
| **Mutabilidad** | Inmutable tras publicar | Editable siempre |
| **Verificabilidad** | Requisito central | No aplica |
| **Resolución** | Automática por motor | Manual (tú cierras la posición) |
| **Datos de mercado** | OHLC intradía hasta 5m | Precio actual y cierre diario |
| **Propósito** | Credibilidad ante terceros | Auto-diagnóstico |

Comparten el catálogo de activos, la capa de datos de mercado y el login de admin.
Todo lo demás es independiente — y el valor conjunto está en el informe de coherencia (§3),
no en ninguno de los dos por separado.
