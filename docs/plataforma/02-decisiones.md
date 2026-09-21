# Registro de decisiones

> Fuente única. Los documentos de módulo enlazan aquí y **no duplican** estas tablas.
> Los IDs son estables: nunca se reutilizan ni se renumeran.

---

## 1. Cómo se usa

- **DT-n** — decisión **tomada**. No se reabre sin motivo nuevo y explícito.
- **Dn** — decisión **abierta**. Ordenadas por *cuándo bloquean*, no por número.
- Cada decisión abierta dice **quién** la decide y **qué etapa bloquea**. Una decisión sin
  fecha de necesidad es una decisión que se puede posponer sin coste.

---

## 2. Decisiones tomadas

| ID | Decisión | Módulo | Razón |
|---|---|---|---|
| **DT-1** | **Web app pública** como frontend, no Discord | 1 | Discord es efímero, no indexa, no se comparte fuera del servidor y se ve mal en pantalla compartida. Discord queda como canal de notificación |
| **DT-2** | Backend en **.NET 10** | Todos | Stack del equipo |
| **DT-3** | El módulo 1 cubre **crypto, equities y forex** desde el inicio | 1 | Cubre todo el contenido del canal. Consecuencia directa: doc 10 §5.5 (normalización entre clases) |
| **DT-4** | El módulo 1 incluye **bot notificador de Discord**, sin captura vía Discord | 1 | Alto valor para la comunidad y bajo coste. La captura es demasiado rica para un modal de Discord |
| **DT-5** | El módulo 2 es **privado**, no público | 2 | Elimina de golpe exposición patrimonial, perfil regulatorio y el requisito de verificabilidad |
| **DT-6** | El módulo 2 usa **captura manual** y la API pública de CoinGecko para precios | 2 | Al ser privado no hay incentivo para falsear. Verificación automática deja de ser requisito |
| **DT-7** | El módulo 2 es un **ledger acotado**, no un portfolio tracker | 2 | ~15% del trabajo y ~90% del valor. Evita cost basis, tax lots, transferencias y fees |
| **DT-8** | El módulo 2 cubre las tres clases con **fuentes gratuitas** | 2 | Solo necesita precio actual y cierre diario, cubierto gratis en las tres (doc 90 §5) |
| **DT-9** | **Planeación conceptual antes que técnica** | Todos | Las reglas de resolución son el 80% del riesgo del producto |

---

## 3. Decisiones abiertas

### 3.1 Bloquean la etapa 1 — hay que cerrarlas ya

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D3** | ¿Histórico retroactivo, o el récord arranca limpio? | 1 | **Alto** — credibilidad del número principal | Tú |
| **D4** | ¿Cuántos targets se permiten y cómo pondera un hit parcial en R? | 1 | Medio — define la expectancy | Tú |
| **D1** | Proveedor de datos para equities y forex | 1 | **Alto** — único coste recurrente real. Investigado en doc 90 | Tú |
| **D2** | Exchange de referencia por símbolo crypto | 1 | Medio — afecta resoluciones en el límite | Tú |
| **D21** | Frontend: **Astro o React** | 1 | Medio — Astro favorece SEO y carga; React favorece el dashboard | Tú |
| **D5** | Confianza en 3 niveles o porcentaje | 1 | Bajo — 3 niveles se captura más rápido | Tú |
| **D6** | Dominio público del sitio | 1 | Bajo | Tú |
| **D22** | **Nombre del programa** y de la plataforma | Todos | Bajo técnicamente, alto para marca | Tú |

### 3.2 Bloquean la etapa 2

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D8** | ¿Se registran posiciones parciales? (ver §4) | 2 | **Alto** — es la puerta de entrada de toda la complejidad | Tú |
| **D7** | Moneda de reporte: USD o MXN | 2 | Medio — si es MXN, toda posición necesita conversión FX diaria | Tú |
| **D9** | ¿El % se captura a mano o se deriva de un valor total declarado? | 2 | Medio — a mano evita rastrear depósitos y retiros | Tú |
| **D10** | ¿Se importa histórico de posiciones pasadas? | 2 | Bajo — al ser privado no hay problema de credibilidad | Tú |

### 3.3 Bloquean la etapa 3 y posteriores

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D11** | **API keys en cliente o en servidor** | 3 | **Máximo** — define el perfil de riesgo entero del SaaS | Tú + seguridad |
| **D13** | Librería de charting y su licencia | 3 | Alto — **bloqueo externo, tramitar con antelación** | Tú |
| **D12** | Qué exchanges en la etapa 3 y en qué orden | 3 | Alto — empezar por aquel donde más operas | Tú |
| **D14** | ¿Spot, futuros, o ambos? | 3 | Alto — futuros multiplica la complejidad (margen, liquidación, funding) | Tú |
| **D16** | ¿La etapa 3 alimenta automáticamente el ledger del módulo 2? | 2+3 | Medio — sinergia evidente, pero acopla los módulos | Tú |
| **D23** | **¿Cuáles son las herramientas diferenciadoras del terminal?** | 3 | **Alto** — es la tesis del módulo 3 y hoy no está definida | Tú |

### 3.4 Requieren asesoría legal — no se deciden en un documento técnico

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D15** | Jurisdicción de la entidad que opera el SaaS | 3 | **Bloqueante** para la etapa 5 | Abogado |
| **D17** | Copy trading: ¿master trader nativo o construcción propia? | 4 | **Máximo** — recomendación fuerte: nativo | Tú + abogado |
| **D18** | Marco regulatorio aplicable al copy trading | 4 | **Bloqueante** para cualquier opción propia | Abogado |
| **D19** | ¿Feed de señales en el terminal? | 4 | Alto — el punto medio con mejor relación valor/riesgo | Tú + abogado |
| **D20** | Forma y ubicación de la divulgación del reparto de fees | 3+4 | Alto — obligatoria en cualquier escenario (PG3) | Tú + abogado |

---

## 4. Notas sobre decisiones concretas

### D8 — posiciones parciales

Es la que hay que vigilar. Las entradas y salidas escalonadas son reales en trading, pero
soportarlas obliga a precio promedio ponderado, cierres parciales y R por tramo. Es la
puerta por la que entra toda la complejidad del portfolio tracker que DT-7 descarta.

**Recomendación: módulo 2 sin parciales.** Una posición es una entrada y una salida. Si al
usarlo resulta insuficiente, se añade después con datos reales de por medio, no con una
suposición de hoy.

### D11 — dónde viven las API keys

La decisión de mayor impacto de todo el programa. Keys en cliente = riesgo de custodia
cero, pero nada funciona con el navegador cerrado. Keys en servidor = habilita
automatización, pero te convierte en un blanco de altísimo valor y una brecha es un evento
de extinción. Desarrollada en doc 30 §3.

**Recomendación: empezar en cliente**, y mover a servidor solo la funcionalidad que lo
exija de verdad, con keys separadas y permisos mínimos.

### D23 — las herramientas diferenciadoras

Declaradas como intención ("herramientas que no he visto en otras apps") pero nunca
descritas. Es la tesis de valor del módulo 3 y **puede reordenar el roadmap**: si son el
motivo real por el que alguien vendría, el MVP del módulo 3 no es un terminal completo sino
esa herramienta, y podría vivir sobre la agregación de solo lectura sin tocar órdenes.

**Siguiente paso: nombrarlas y describirlas.** Merecen su propio documento (`50-`).

---

## 5. Decisiones que ya no se tomarán

Registradas para no reabrir discusiones cerradas.

| Descartado | Por qué |
|---|---|
| Discord como frontend principal | DT-1 |
| Portfolio público (con o sin montos) | Expone patrimonio, agrava el perfil regulatorio y exige verificación on-chain para valer algo. Sustituido por el feed de trades del módulo 3a, que sí es verificable |
| Leer el portfolio desde la API de CoinGecko | **No existe ese endpoint.** La API sirve datos de mercado, no holdings de usuario (doc 90 §2) |
| Alpha Vantage como proveedor de datos | 25 llamadas/día en el plan gratuito. Inservible (doc 90 §3) |
| Estado "Superseded" para proyecciones | Sería la puerta trasera para retirar las perdedoras antes de que fallen (doc 10 §4.4) |
