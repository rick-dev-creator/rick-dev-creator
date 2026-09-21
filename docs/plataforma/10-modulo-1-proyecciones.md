# Módulo 1 — Proyecciones Públicas

> Documento conceptual. Define **qué tiene que hacer la app** y **bajo qué reglas**, no cómo se construye.
> Las decisiones técnicas (.NET 10, Astro/React) se registran al final como restricciones conocidas,
> pero no condicionan el modelo.

---

## 1. Propósito

Llevar un **récord histórico, público y auditable** del análisis técnico que se publica
semanalmente en YouTube, de forma que cualquier tercero pueda verificar, sin confiar en el
analista, qué se dijo, cuándo se dijo, y si resultó correcto.

No es una app de trading: **dentro de este módulo** no hay señales, ni ejecución de
órdenes, ni gestión de portafolio. Eso vive en los módulos 2 y 3 (ver
[00-vision-y-roadmap.md](./00-vision-y-roadmap.md)). El módulo 1 es un **sistema de
registro y calificación de predicciones**, y nada más.

### El problema real que resuelve

Todo analista de TA en redes tiene el mismo problema de credibilidad: el sesgo de
supervivencia. Se recuerdan los aciertos, los fallos se diluyen. Nadie puede demostrar lo
contrario porque no existe el registro.

La app existe para hacer ese registro **imposible de manipular**. Ese es el producto.
Un tracker editable no vale nada; uno inmutable vale mucho.

### Criterios de éxito

1. Registrar una proyección toma **menos de 2 minutos**. Si toma más, se abandona el hábito.
2. El **90%+ de las proyecciones se resuelven solas**, sin intervención humana.
3. La página pública es **presentable en un live stream** sin edición previa.
4. Un escéptico puede auditar cualquier proyección hasta el video original.

---

## 2. Principios de diseño

Estos cuatro principios mandan sobre cualquier decisión posterior. Si una funcionalidad los
contradice, la funcionalidad se descarta.

### P1 — Falsabilidad obligatoria

Una proyección que no puede fallar no se registra. En el momento de capturar, el sistema
exige los datos que permiten declararla incorrecta. Sin invalidación ni horizonte, no hay
proyección: hay opinión.

### P2 — Inmutabilidad tras publicación

Una proyección publicada **nunca se edita ni se borra**. Se puede versionar (corregir un
typo, adjuntar una imagen), pero:

- La versión original queda visible en el historial público.
- Los campos de calificación (dirección, targets, invalidación, horizonte) son inmutables.
  Cambiar la tesis crea una **proyección nueva**, no modifica la vieja.
- La proyección original se resuelve igual, con sus reglas originales.

Esto cierra la única vía real de hacer trampa: "actualizar" una tesis perdedora para que
deje de contar.

### P3 — Resolución automática y determinista

El resultado lo decide el motor contra datos de mercado, no el analista. Las reglas se
publican y son las mismas para todas las proyecciones. Donde los datos son ambiguos, la
regla resuelve **en contra del analista** (ver §5.4). Un récord que se autocalifica no es
un récord.

### P4 — Transparencia radical

Todo es público: las proyecciones activas, las resueltas, las fallidas, las reglas de
resolución, el historial de ediciones y los datos de mercado usados para resolver cada una.
No existe "borrador oculto que salió mal".

---

## 3. Actores y casos de uso

### Actores

| Actor | Descripción | Acceso |
|---|---|---|
| **Analista** | Único usuario autenticado. Captura y publica proyecciones. | Admin privado |
| **Audiencia** | Comunidad de Discord y visitantes del sitio. Solo consulta. | Público, sin login |
| **Motor de resolución** | Proceso automático. Ingiere precios y resuelve proyecciones. | Sistema |
| **Notificador Discord** | Bot que anuncia eventos del ciclo de vida en el servidor. | Sistema |

### Casos de uso — Analista

- **CU-A1** Capturar una proyección (tras grabar/durante el video semanal).
- **CU-A2** Publicarla, sellándola contra el video de YouTube (ID + timestamp).
- **CU-A3** Adjuntar el snapshot del gráfico con el análisis marcado.
- **CU-A4** Consultar sus proyecciones activas antes del siguiente video.
- **CU-A5** Revisar el scorecard: dónde acierta y dónde falla, por activo y timeframe.
- **CU-A6** Anular una proyección **solo antes de publicarla**. Después, imposible.
- **CU-A7** Resolver manualmente los casos que el motor no puede (excepción, §5.6).

### Casos de uso — Audiencia

- **CU-P1** Ver qué proyecciones están activas ahora mismo y su progreso hacia el target.
- **CU-P2** Ver el récord histórico completo, incluyendo los fallos.
- **CU-P3** Filtrar por activo, timeframe, dirección y periodo.
- **CU-P4** Abrir una proyección y llegar al minuto exacto del video donde se dijo.
- **CU-P5** Verificar que no fue editada después de publicarse (historial de versiones).
- **CU-P6** Recibir aviso en Discord cuando algo se resuelve.

### Casos de uso — Stream

- **CU-S1** Mostrar en pantalla, en formato legible a distancia, las proyecciones de la
  semana pasada y su resultado.
- **CU-S2** Mostrar el scorecard acumulado como referencia en vivo.

---

## 4. Modelo de dominio

### 4.1 Entidades

```
Projection (raíz de agregado)
├── Asset                 qué activo
├── Thesis                qué se afirma
├── TargetLevel[]         a dónde va (1..n)
├── InvalidationLevel     qué la mata
├── Horizon               para cuándo
├── Provenance            dónde se dijo (video, timestamp, live)
├── Confidence            qué tan seguro estaba
├── Resolution            cómo terminó (null mientras está activa)
└── VersionHistory[]      qué cambió y cuándo (append-only)

Asset
├── Symbol                BTCUSDT, AAPL, EURUSD
├── AssetClass            Crypto | Equity | Forex
├── ReferenceSource       de qué fuente se lee el precio (§6.1)
└── QuoteCurrency

Candle (serie temporal)
└── Symbol, Source, Timeframe, OpenTime, O, H, L, C, V
```

### 4.2 Tipos de proyección

No todo el TA es "compro aquí, vendo allá". El módulo 1 soporta tres formas, cada una con su
propia lógica de resolución:

| Tipo | Afirma | Se resuelve con |
|---|---|---|
| **Direccional** | "X va a subir/bajar hasta T sin romper I" | Target + invalidación + horizonte |
| **Rango** | "X se queda entre A y B hasta la fecha F" | Fronteras del rango + horizonte |
| **Test de nivel** | "X toca el nivel N antes de la fecha F" | Nivel + horizonte (sin invalidación) |

La **direccional** es el caso principal y el único que produce métricas en R (§7.2). Las
otras dos existen porque forzar todo análisis al molde direccional hace que se dejen de
registrar proyecciones legítimas — y una proyección no registrada es peor que una fallida.

### 4.3 Anatomía de una proyección direccional

| Campo | Obligatorio | Por qué existe |
|---|---|---|
| Activo | Sí | Qué se analiza |
| **Actividad** | **Sí** | **Trading o Investing.** Son dos disciplinas con horizontes, R y tasas de acierto incomparables. Mezclarlas en un mismo scorecard produce métricas sin significado |
| Timeframe | Sí | 1H / 4H / 1D / 1W. Contexto del análisis y base del horizonte por defecto |
| Dirección | Sí | Alcista / Bajista |
| Precio de referencia | Sí | Precio al momento de publicar. Ancla para medir R y progreso |
| Targets (T1..T3) | Sí (min. 1) | A dónde se espera que llegue |
| Regla de target | Sí | `Toque` o `Cierre` (§5.2) |
| **Nivel de invalidación** | **Sí** | **El precio que mata la tesis. Sin esto no hay proyección** |
| Horizonte | Sí | Fecha límite. Sin esto nada se resuelve jamás |
| Confianza | Sí | Baja / Media / Alta. Habilita el análisis de calibración (§7.3) |
| Tesis | Sí | Narrativa en texto. Lo que dijiste, en tus palabras |
| Snapshot del gráfico | Recomendado | Evidencia visual del análisis en el momento |
| Video + timestamp | Sí al publicar | Prueba externa de cuándo se dijo |

**Sobre el horizonte y los "tiempos aproximados":** el sistema propone un horizonte por
defecto derivado del timeframe (1H → 3 días, 4H → 10 días, 1D → 30 días, 1W → 90 días),
editable siempre. Es un **deadline duro**, no un rango difuso: si llega la fecha sin
resolución, expira. Un horizonte flexible es un horizonte que nunca vence, y una proyección
que nunca vence nunca falla.

### 4.4 Ciclo de vida

```
                 ┌─────────┐
                 │  Draft  │  editable y borrable
                 └────┬────┘
                      │ publicar (sella video + precio de referencia)
                      ▼
                 ┌─────────┐
                 │ Active  │  INMUTABLE desde aquí
                 └────┬────┘
                      │ el motor evalúa cada vela nueva
        ┌─────────┬───┴────┬──────────────┐
        ▼         ▼        ▼              ▼
    ┌──────┐ ┌────────┐ ┌─────────────┐ ┌─────────┐
    │ Hit  │ │Partial │ │ Invalidated │ │ Expired │
    └──────┘ └────────┘ └─────────────┘ └─────────┘
       T3       T1/T2      rompió I      venció el
     alcanzado  alcanzado                  plazo
```

Estados terminales. No hay vuelta atrás ni reapertura.

**No existe un estado "Superseded".** Si en un video posterior la tesis cambia, se registra
una proyección nueva y la anterior sigue su curso hasta resolverse con sus reglas
originales. Permitir "sustituir" una proyección sería la puerta trasera para retirar las
perdedoras antes de que fallen.

---

## 5. Reglas de resolución

Esta sección **es el producto**. Todo lo demás es interfaz alrededor de ella. Estas reglas
se publican en el sitio para que cualquiera pueda auditar las calificaciones.

### 5.1 Precedencia de eventos

En orden, la primera condición que se cumple resuelve la proyección:

1. ¿Se rompió la invalidación? → `Invalidated`
2. ¿Se alcanzó el último target? → `Hit`
3. ¿Venció el horizonte? → `Hit parcial` si alcanzó T1+, `Expired` si no alcanzó ninguno

### 5.2 Toque vs cierre

Se define por proyección al capturar:

- **Toque** — basta que el `High` (alcista) o `Low` (bajista) de una vela alcance el nivel.
- **Cierre** — el `Close` de una vela del timeframe de la proyección debe superar el nivel.

El defecto es **Toque para targets** y **Cierre para invalidación**. Es la convención que
más se parece a cómo opera la gente real: se toma ganancia con una mecha, pero no se
abandona la tesis por una mecha.

### 5.3 El problema del gap

En equities (y en forex el fin de semana) el precio salta sin operar en medio. Reglas:

- Si el mercado **abre más allá del target**, cuenta como `Hit`, pero el precio de
  ejecución registrado es el **de apertura**, no el del target. Si no, el récord infla los
  resultados con ganancias que nadie pudo capturar.
- Si el mercado **abre más allá de la invalidación**, es `Invalidated`, con la pérdida real
  al precio de apertura (peor que la planeada). Simétrico.
- En crypto no aplica: mercado continuo.

### 5.4 La regla conservadora (la más importante)

**Si en una misma vela el precio toca el target y la invalidación, la proyección se resuelve
como `Invalidated`.**

Una vela no dice en qué orden ocurrieron el máximo y el mínimo. El motor intenta primero
desempatar bajando al timeframe inferior disponible (1D → 1H → 5m). Si con el dato más fino
sigue habiendo ambigüedad, **gana la invalidación**.

Es deliberadamente desfavorable al analista. Ese sesgo en contra es lo que hace que el
número publicado sea creíble: el récord real solo puede ser igual o mejor que el publicado,
nunca peor.

### 5.5 Normalización entre clases de activo

Cubrir crypto + equities + forex desde el día 1 significa que "el precio tocó X" tiene tres
significados distintos. Hay que decidirlos explícitamente:

| | Crypto | Equities | Forex |
|---|---|---|---|
| **Horario** | 24/7 continuo | Sesión regular con apertura/cierre | Domingo 17:00 ET → viernes 17:00 ET |
| **El horizonte cuenta en** | Días naturales | Días hábiles de mercado | Días hábiles de mercado |
| **Pre/post market** | N/A | **No cuenta.** Solo sesión regular | N/A |
| **Gaps** | No hay | Frecuentes (apertura, earnings) | Solo apertura del domingo |
| **Problema del precio único** | El precio difiere entre exchanges | Consolidado, poco problema | No hay precio central: depende del broker |
| **Corrupción del histórico** | Ninguna | **Splits y dividendos** | Ninguna |

Tres decisiones que se derivan de esto:

1. **Fuente de referencia fijada por símbolo y sellada al publicar.** Cada proyección
   guarda de qué fuente se lee su precio. Cambiar la fuente después reescribiría el pasado.
2. **Precios sin ajustar + factor de ajuste por separado.** Un split 4:1 divide entre 4 los
   precios históricos y convertiría una proyección acertada en fallida. Los niveles de la
   proyección se evalúan siempre contra el precio tal como cotizó ese día.
3. **Solo sesión regular en equities.** Un target alcanzado en after-hours con volumen
   irrisorio no es un target alcanzado.

### 5.6 Resolución manual (la excepción)

Casos donde el motor no puede decidir: símbolo deslistado, fusión o adquisición, redenominación
de un par, hueco de datos del proveedor.

Cuando ocurre, la proyección entra en cola de revisión y el analista la resuelve a mano.
Esa resolución queda **marcada públicamente como manual, con justificación obligatoria**,
y las métricas muestran el conteo de resoluciones manuales. Si ese número crece, es señal
de un problema — y la audiencia lo ve tan claro como el analista.

---

## 6. Datos de mercado

### 6.1 Requisitos conceptuales

- **Velas OHLC** por símbolo y timeframe, hasta granularidad de 5 minutos para el desempate
  de §5.4.
- **Una sola fuente de referencia por símbolo**, declarada y sellada en la proyección.
- **Histórico propio.** Toda vela consultada se guarda. Con el tiempo, el sistema deja de
  depender del proveedor para reconstruir resoluciones pasadas, y cualquiera puede
  reevaluar el récord completo con los mismos datos que se usaron.
- **Solo se consultan símbolos con proyecciones activas.** El costo escala con lo que
  realmente se analiza, no con el universo de activos.

### 6.2 Riesgo abierto

Es la dependencia externa más frágil del proyecto y la principal razón por la que cubrir
las tres clases de activo desde el inicio sube el costo: crypto tiene datos gratuitos y
excelentes, equities y forex no. La elección de proveedor(es) se resuelve en el documento
técnico; conceptualmente lo que importa es que el modelo **no asuma un proveedor único**.

---

## 7. Métricas y scorecard

### 7.1 Métricas básicas

**Toda métrica se segmenta primero por actividad (trading / investing).** Un hit rate que
mezcla una entrada de 4H con una tesis de doce meses no dice nada sobre ninguna de las dos.
El número global existe, pero el que se lee es el segmentado.

- Hit rate global y desglosado por actividad, activo, clase de activo, timeframe y dirección.
- Distribución de resultados: Hit / Parcial / Invalidada / Expirada.
- Mediana de tiempo hasta resolución.
- Rachas actuales y máximas.
- Proyecciones activas ahora y su progreso hacia el target.

### 7.2 Métricas en R (lo que da autoridad al récord)

Con el nivel de invalidación ya capturado, cada proyección tiene un riesgo/beneficio
implícito:

```
R = (target − referencia) / (referencia − invalidación)
```

De ahí sale la **expectancy**: `(hit_rate × R_promedio_ganador) − (miss_rate × 1)`.

Esto cambia por completo la conversación. "Acerté el 55%" es un número que no significa
nada solo. "Acerté el 55% con expectancy de +0.7R" es una afirmación verificable y mucho
más fuerte — y permite un récord honesto con hit rate bajo, si los aciertos son grandes.

Se acompaña de una **curva de equity hipotética** arriesgando 1R por proyección. Es la
gráfica que mejor comunica un track record y la que se muestra en el stream.

### 7.3 Calibración (el diferenciador)

Cruzar el nivel de confianza declarado contra el hit rate real:

```
Confianza Alta   → 40 proyecciones → 72% acierto
Confianza Media  → 65 proyecciones → 54% acierto
Confianza Baja   → 22 proyecciones → 38% acierto
```

Si la curva es monótona creciente, demuestra algo que casi nadie puede demostrar: que el
analista **sabe cuándo sabe**. Es una afirmación mucho más difícil de fingir que un hit
rate, y prácticamente ningún canal de trading la publica.

### 7.4 Higiene de las métricas

- Las proyecciones **activas nunca cuentan** en el scorecard. Solo las resueltas.
- Las **expiradas cuentan** como no-acierto. Excluirlas sería el sesgo de supervivencia por
  la puerta de atrás.
- Toda métrica muestra su **tamaño de muestra**. Un 80% sobre 5 proyecciones no es un dato.
- Cualquier importación retroactiva se marca visiblemente y se excluye del número
  principal.

---

## 8. Superficies de la aplicación

### 8.1 Admin privado (solo analista)

Formulario de captura optimizado para velocidad: activo reciente en un clic, precio de
referencia autocompletado en vivo, horizonte sugerido por timeframe, R calculado en
pantalla mientras se escriben los niveles. Objetivo: **menos de 2 minutos** por proyección.
Más lista de activas, cola de resolución manual y publicación en lote al final del video.

### 8.2 Sitio público

- **Portada** — scorecard, curva de equity, proyecciones activas.
- **Listado** — todo el histórico, filtrable, con los fallos tan visibles como los aciertos.
- **Detalle** — tesis, niveles, snapshot, resultado, enlace al minuto exacto del video, y
  el historial de versiones que prueba que no se editó.
- **Reglas** — las reglas de §5, publicadas.

### 8.3 Modo stream

Vista aparte, tipografía grande, alto contraste, sin navegación. Pensada para compartir
pantalla: "proyecciones de la semana pasada y cómo salieron", más el scorecard acumulado.
No es la vista pública con zoom; es una vista propia con su propio diseño.

### 8.4 Bot de Discord (notificador)

Sin captura de datos. Solo anuncia, con enlace al detalle público:

- Proyección publicada.
- Proyección resuelta (Hit / Parcial / Invalidada / Expirada).
- Aviso de proximidad: precio cerca del target o de la invalidación.
- Aviso de expiración próxima.
- Resumen semanal antes del live.

---

## 9. Flujo semanal completo

1. **Antes del video** — el analista revisa las activas y el resumen semanal del bot.
2. **Durante la grabación** — hace el análisis normalmente, sin cambiar nada de su proceso.
3. **Después de publicar el video** — captura las proyecciones en el admin (~2 min c/u),
   marca el timestamp del video en cada una y publica en lote. Quedan selladas.
4. **El bot anuncia** en Discord las nuevas proyecciones.
5. **Durante la semana** — el motor ingiere precios y resuelve automáticamente. El bot
   anuncia cada resolución conforme ocurre.
6. **En el siguiente live** — abre el modo stream y repasa qué salió y qué no, con el
   récord actualizado en pantalla.

---

## 10. Fuera del alcance del módulo 1

Se excluye explícitamente para proteger el criterio de "presentable en el próximo live":

- Múltiples analistas o cuentas de comunidad.
- Comentarios, votaciones o predicciones de la audiencia.
- Captura de proyecciones desde Discord.
- Alertas de precio personalizadas por usuario.
- Backtesting o portafolio simulado con dinero real.
- Integración automática con TradingView.
- Proyecciones relativas (A rinde más que B) y de tipo "cesta".
- App móvil.

---

## 11. Decisiones abiertas

Las decisiones abiertas de este módulo (D1–D6) viven en el registro único:
**[02-decisiones.md](./02-decisiones.md)**. No se duplican aquí para que no se
desincronicen.

---

## 12. Restricciones técnicas conocidas

Registradas aquí para no perderlas. Se desarrollan en el documento técnico, no en éste.

- **Backend**: .NET 10.
- **Frontend público**: Astro o React. A definir.
- **Alcance de activos**: crypto, equities y forex desde el inicio (§5.5 es su consecuencia
  directa).
- **El módulo 1 incluye** bot de Discord notificador, sin captura por Discord.
- La inmutabilidad de §P2 encaja de forma natural con un modelo append-only /
  event-sourced, pero el volumen esperado (~5-20 proyecciones/semana) no lo exige. La
  decisión queda abierta.

---

## 13. Los tres riesgos reales

1. **Definir "correcta" es el 80% del proyecto.** El §5 es donde se gana o se pierde.
   Recomendación antes de escribir código: tomar 10 proyecciones reales de videos pasados y
   resolverlas a mano con estas reglas. Los desacuerdos que aparezcan son los huecos del
   modelo.
2. **La fricción de captura mata el hábito.** Si registrar la semana toma 20 minutos, en un
   mes no se usa. El presupuesto de 2 minutos por proyección es un requisito, no un deseo.
3. **Cubrir tres clases de activo desde el día 1 triplica la superficie de datos** antes de
   que el motor esté probado. Mitigación sugerida: construir el motor completo y probarlo
   con crypto, que da datos limpios y gratuitos, y habilitar equities y forex en cuanto
   pase esas pruebas — sin cambiar el alcance, solo el orden de validación.
