# Herramientas diferenciadoras de Desk

> Resuelve parcialmente **D23**. Es la tesis de valor del módulo 3.
> Documento abierto — falta enumerar el resto de herramientas (§4).

---

## 1. Por qué un terminal propio

La razón real es una sola, y es mejor que las dos que parecían:

> Las herramientas propias existen para **mejorar tu performance**, tanto en investing como
> en trading.

Eso cambia la naturaleza del módulo 3: **el terminal es un instrumento personal primero y un
producto después.** El SaaS es consecuencia, no causa.

Es la mejor motivación posible para construirlo:

1. **El usuario mejor entendido eres tú.** Construir para uno mismo produce mejores
   productos que construir para un mercado imaginado, sobre todo en herramientas de
   trading, donde el detalle que importa solo se conoce operando.
2. **Valida la secuencia del roadmap.** Solo lectura → ejecución propia → SaaS no es
   prudencia burocrática: es el orden natural de algo que existe para ti y que solo después
   se abre.
3. **Tienes un criterio de éxito medible**, que casi ningún producto tiene: ¿mejoró tu
   performance? El módulo 2 responde esa pregunta con números.

Y descarta la justificación que no sostiene el proyecto: **ahorrar suscripciones ajenas**.
Una suscripción cuesta mucho menos que los meses de ingeniería que esto lleva. Es un buen
efecto secundario, nunca la tesis.

**Consecuencia de alcance:** el MVP del módulo 3 no es un terminal completo, es la primera
herramienta que corrige un fallo concreto de tu operativa, montada sobre la agregación de
solo lectura. No compitas donde los productos maduros ya están bien.

---

## 2. H1 — Escalera de órdenes piramidal, visual

La primera herramienta declarada, y sirve de arquetipo para las demás.

### Qué es

Colocar N órdenes límite distribuidas en un rango de precio, con una curva de tamaños
configurable, para longs y shorts, manipulada **visualmente sobre el gráfico**.

### Qué falla hoy que esto corrige

Calidad de ejecución: entrar de golpe a un solo precio, o perseguir el precio a mano cuando
se mueve. Una escalera te obliga a decidir el rango y el tamaño **antes**, en frío.

### Dónde se quedan cortos los exchanges

Varios ofrecen *scaled orders* básicas, pero:

- Se configuran en un formulario, no arrastrando sobre el gráfico.
- No te muestran **en vivo, mientras ajustas**, cuál sería tu entrada promedio resultante.
- No relacionan la escalera con tu nivel de invalidación ni con el R resultante.
- No tratan la escalera como **una sola cosa**: una vez enviada son N órdenes sueltas que
  hay que mover o cancelar una por una.

### Lo que la hace valiosa

El preview en vivo mientras arrastras: **entrada promedio, tamaño total, R resultante contra
tu invalidación y, en apalancado, precio de liquidación.** Eso es lo que ningún exchange te
enseña mientras armas la escalera, y es justo lo que necesitas para decidir si está bien
puesta.

### La buena noticia arquitectónica

Las órdenes de la escalera son **órdenes límite nativas del exchange**, en reposo en su
libro. No son sintéticas ni dependen de que tu backend esté vivo.

Consecuencia: **tu herramienta insignia funciona con las keys en el cliente** (D11) y
sobrevive a que tu servidor se caiga. La funcionalidad más diferenciadora del producto no te
obliga a la arquitectura arriesgada.

### Modelado: la escalera es un objeto de primera clase

El exchange solo conoce órdenes individuales. Tú necesitas moverla, cancelarla y evaluarla
**como una unidad**:

```
OrderLadder
├── Activity             Trading | Investing (§5)
├── Direction            Long | Short
├── PriceRange           desde / hasta
├── LevelCount           N órdenes
├── Distribution         Uniforme | Lineal | Geométrica | Personalizada
├── TotalSize            tamaño agregado
├── ChildOrders[]        → IDs reales en el exchange
├── LinkedProjection     → Projection del módulo 1 (§2.1)
└── State                Borrador | Activa | ParcialmenteLlena | Completada | Cancelada
```

La **distribución** es lo que la vuelve "piramidal": más tamaño conforme el precio mejora,
o lo contrario según la estrategia. Configurable y guardable como plantilla reutilizable.

### 2.1 La integración que nadie más puede copiar

Tu terminal tiene algo que ningún competidor tiene: **tus proyecciones del módulo 1 viven en
el mismo sistema.**

Eso permite precargar una escalera desde una proyección publicada: la zona de entrada define
el rango, la **invalidación define el stop loss automáticamente**, y los targets definen la
escalera de salida. Un clic desde tu propio análisis a la orden puesta.

Es integración vertical pura y es inimitable: un terminal genérico no sabe cuál es tu tesis.

### 2.2 Lo genuinamente difícil

No es dibujar la escalera. Es esto:

- **Notional mínimo por orden.** Partir el tamaño en 10 puede dejar cada tramo por debajo
  del mínimo del exchange. Hay que avisarlo *antes* de enviar, no fallar a la mitad.
- **Redondeo de tick y lot size.** Distribuir tamaño acumula deriva; el agregado real
  difiere del pedido. Hay que decidir dónde se absorbe la diferencia.
- **Llenados parciales.** La entrada promedio cambia en cada fill. El R mostrado tiene que
  recalcularse en vivo o miente.
- **No hay atomicidad.** Mover o cancelar una escalera son N llamadas que pueden fallar a
  la mitad. Necesitas estado de reconciliación explícito, no asumir éxito.
- **Límite de órdenes abiertas** por símbolo y por cuenta, distinto en cada exchange.
- **Reduce-only y modo de posición** en shorts y futuros, con semántica distinta por
  exchange.

Ninguno es insalvable. Todos son la razón por la que esto tarda más de lo que parece.

---

## 3. El bucle de performance

El terminal no mejora tu performance por sí solo. La mejora sale de cerrar el circuito con
los otros dos módulos:

```
   Módulo 1  ──►  Módulo 3  ──►  Módulo 2  ──┐
   lo que        lo que          ¿coincidieron?  │
   dijiste       hiciste         ¿por qué no?    │
      ▲                                          │
      └──────────────────────────────────────────┘
          mejores tesis y mejores herramientas
```

Por eso el módulo 2, que parecía el más prescindible, es el que convierte al 3 en una
inversión medible en vez de un capricho: es el único que puede decirte si la herramienta
nueva sirvió de algo.

---

## 4. Cómo enumerar el resto: empieza por el problema, no por la función

**Esta sección está vacía a propósito y es el siguiente paso del módulo 3.**

Una herramienta que no corrige un fallo concreto de tu operativa es una función más, no una
ventaja. Los fallos de performance caen en cinco categorías, y cada una implica un tipo de
herramienta distinto y —lo que más importa— **un coste y un riesgo distintos**:

| Problema | Síntoma | Herramienta típica | ¿Lógica en servidor? | Riesgo |
|---|---|---|---|---|
| **Calidad de ejecución** | Entras peor que lo planeado, persigues precio | Escaleras, órdenes compuestas, preview de promedio | No | Bajo |
| **Dimensionamiento** | Tamaños inconsistentes, no sizeas a tu convicción | Size derivado de R, riesgo máximo por operación | No | Bajo |
| **Disciplina** | Mueves stops, sales antes, entras por FOMO | Stop nativo obligatorio, bloqueo de entradas sin invalidación, checklist previo | No | Bajo |
| **Carga cognitiva** | Recalculas R y promedios a mano, bajo presión | Todo lo anterior, en vivo sobre el gráfico | No | Bajo |
| **Atención y latencia** | Te pierdes setups, no puedes vigilar todo | Alertas, entradas condicionales, automatización | **Sí** | **Alto** |

**Los cuatro primeros son los que más performance dan y ninguno requiere lógica viva en
servidor.** El quinto es el único caro y arriesgado.

Si al enumerar tus herramientas resulta que casi todas atacan los cuatro primeros —lo típico
en un operador discrecional— entonces **el módulo 3 es mucho más barato y seguro de lo que
parecía, y D11 se resuelve solo**: keys en cliente, sin custodia, sin responsabilidad de
ejecución.

### Las cuatro preguntas por herramienta

1. **Qué fallo corrige**, de las cinco categorías de arriba.
2. **Qué hace hoy el exchange** y dónde exactamente se queda corto.
3. **¿Órdenes nativas o lógica viva en servidor?**
4. **¿Se conecta con el módulo 1?** Si sí, es inimitable.

---

## 5. Investing y trading no necesitan las mismas herramientas

Las herramientas son para ambas disciplinas, pero conviene no asumir que son las mismas:

| | Trading | Investing |
|---|---|---|
| Horizonte | Intradía a semanas | Meses a años |
| Urgencia de ejecución | Alta | Baja |
| Fallo a corregir | Precisión y disciplina bajo presión | Constancia y no desviarse del plan |
| Herramientas típicas | Escalera piramidal, gestión de stop, R en vivo | Acumulación programada, órdenes que reposan meses, deriva de asignación, rebalanceo |

**La escalera es el puente entre ambas.** Una pirámide de límites muy separada y sin
caducidad *es* una estrategia de acumulación: órdenes que esperan una caída durante meses.
Mismo primitivo, distintos parámetros y horizonte.

Eso sugiere construir el primitivo bien **una vez** y parametrizarlo por actividad, en vez
de dos herramientas separadas. Y refuerza empezar por H1: es la que sirve a las dos.

Y por PG7: **sus métricas no se mezclan jamás.** Un hit rate que promedia una entrada de 4H
con una tesis de doce meses no describe ninguna de las dos.
