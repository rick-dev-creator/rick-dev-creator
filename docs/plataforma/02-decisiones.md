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
| **DT-7** | El módulo 2 es un **journal acotado**, no un portfolio tracker | 2 | ~15% del trabajo y ~90% del valor. Evita cost basis, tax lots, transferencias y fees |
| **DT-8** | El módulo 2 cubre las tres clases con **fuentes gratuitas** | 2 | Solo necesita precio actual y cierre diario, cubierto gratis en las tres (doc 90 §5) |
| **DT-9** | **Planeación conceptual antes que técnica** | Todos | Las reglas de resolución son el 80% del riesgo del producto |
| **DT-10** | **El copy trading se delega a los exchanges** (master trader nativo). Cierra D17 | 4 | Mismo beneficio, cero riesgo legal, cero ingeniería. El exchange es la entidad regulada (doc 40 §2) |
| **DT-11** | Los **avisos de inversión** van a Discord y son beneficio de tier alto, **no públicos**. Cierra D19 | 5 | La prueba es pública, la oportunidad es de pago (PG6) |
| **DT-12** | La primera herramienta diferenciadora es la **escalera de órdenes piramidal visual** | 3 | Usa órdenes nativas: funciona con keys en cliente y sobrevive a caídas (doc 35 §2) |
| **DT-13** | El terminal se justifica por **mejorar tu performance**, no por ahorrar suscripciones | 3 | Instrumento personal primero, producto después. Da un criterio de éxito medible vía módulo 2 |
| **DT-15** | **Todo en inglés**: código, namespaces e interfaz pública | Todos | Convención estándar, alcance global y alineado con la cultura del sector |
| **DT-16** | Nombres: **Calls · Journal · Desk · Club**. El número sigue siendo el ID estable | Todos | Registro trading-native, como habla la comunidad. El módulo 4 no recibe nombre |
| **DT-14** | **Actividad (Trading / Investing) es dimensión de primera clase** en los módulos 1, 2 y 3 | 1+2+3 | Derivada de DT-13: son disciplinas con horizontes y R incomparables. Mezclarlas da métricas falsas (PG7). Barata ahora, migración después |

---

## 3. Decisiones abiertas

### 3.1 Bloquean la etapa 1 — módulo 1, hay que cerrarlas ya

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D3** | ¿Histórico retroactivo, o el récord arranca limpio? | 1 | **Alto** — credibilidad del número principal | Tú |
| **D4** | ¿Cuántos targets se permiten y cómo pondera un hit parcial en R? | 1 | Medio — define la expectancy | Tú |
| **D1** | Proveedor de datos para equities y forex | 1 | **Alto** — único coste recurrente real. Investigado en doc 90 | Tú |
| **D2** | Exchange de referencia por símbolo crypto | 1 | Medio — afecta resoluciones en el límite | Tú |
| **D21** | Frontend: **Astro o React** | 1 | Medio — Astro favorece SEO y carga; React favorece el dashboard | Tú |
| **D5** | Confianza en 3 niveles o porcentaje | 1 | Bajo — 3 niveles se captura más rápido | Tú |
| **D6** | Dominio público del sitio | 1 | Bajo | Tú |
| **D22** | **Nombre del programa**: marca nueva, independiente del canal (ver §4) | Todos | Bajo técnicamente, alto para marca | Tú |

### 3.2 Bloquean las etapas 2 y 3 — módulo 5, es donde está el dinero

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D24** | **Umbral de calificación por volumen** (ver §4) | 5 | **Alto** — un umbral alto empuja a tu comunidad a operar de más | Tú |
| **D26** | ¿Patreon se mantiene, se sustituye por suscripción propia, o conviven? | 5 | **Alto** — parte de tu base no podrá calificar por volumen (doc 50 §4) | Tú |
| **D28** | Matriz de beneficios por tier: qué desbloquea cada nivel | 5 | Alto — define el producto de membresía | Tú |
| **D27** | Qué exchanges se integran primero para verificar afiliación | 5 | Medio — OKX es el mejor documentado | Tú |
| **D25** | TradingView: ¿semi-automático o automatizado? | 5 | Medio — automatizar exige desactivar tu 2FA (ver §4) | Tú |

### 3.3 Bloquean la etapa 4 — módulo 2

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D8** | ¿Se registran posiciones parciales? (ver §4) | 2 | **Alto** — es la puerta de entrada de toda la complejidad | Tú |
| **D7** | Moneda de reporte: USD o MXN | 2 | Medio — si es MXN, toda posición necesita conversión FX diaria | Tú |
| **D9** | ¿El % se captura a mano o se deriva de un valor total declarado? | 2 | Medio — a mano evita rastrear depósitos y retiros | Tú |
| **D10** | ¿Se importa histórico de posiciones pasadas? | 2 | Bajo — al ser privado no hay problema de credibilidad | Tú |

### 3.4 Bloquean las etapas 5 a 7 — módulo 3

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D23** | **El resto de herramientas diferenciadoras** (ver §4) | 3 | **Alto** — es la tesis del módulo 3. Parcialmente resuelta por DT-12 | Tú |
| **D29** | **¿Qué fallo de performance se ataca primero?** (ver §4) | 3 | **Alto** — define el MVP del módulo 3 y, de rebote, resuelve D11 | Tú |
| **D11** | **API keys en cliente o en servidor** | 3 | **Máximo** — define el perfil de riesgo entero del SaaS | Tú + seguridad |
| **D13** | Librería de charting y su licencia | 3 | Alto — **bloqueo externo, tramitar con antelación** | Tú |
| **D12** | Qué exchanges en la etapa 5 y en qué orden | 3 | Alto — empezar por aquel donde más operas | Tú |
| **D14** | ¿Spot, futuros, o ambos? | 3 | Alto — futuros multiplica la complejidad (margen, liquidación, funding) | Tú |
| **D16** | ¿La etapa 5 alimenta automáticamente el Journal del módulo 2? | 2+3 | Medio — sinergia evidente, pero acopla los módulos | Tú |

### 3.5 Requieren asesoría legal — no se deciden en un documento técnico

| ID | Decisión | Mód. | Impacto | Decide |
|---|---|---|---|---|
| **D15** | Jurisdicción de la entidad que opera el SaaS | 3 | **Bloqueante** para la etapa 7 | Abogado |
| **D18** | Marco regulatorio aplicable si algún día se construye copy trading | 4 | Latente — DT-10 lo aplaza, no lo elimina | Abogado |
| **D20** | Forma y ubicación de la divulgación del reparto de fees | 3+5 | Alto — obligatoria en cualquier escenario (PG3) | Tú + abogado |

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
exija de verdad, con keys separadas y permisos mínimos. **DT-12 la refuerza**: la escalera
piramidal usa órdenes nativas del exchange, así que la herramienta insignia no obliga a la
arquitectura arriesgada.

### D23 — el resto de herramientas diferenciadoras

**Parcialmente resuelta.** DT-12 fija la primera: la escalera de órdenes piramidal visual,
descrita en doc 35 §2. Faltan las demás, declaradas como intención pero sin describir.

Para cada una bastan cuatro respuestas (doc 35 §3), y **la tercera es la que más importa**:
¿usa órdenes nativas del exchange, o requiere lógica viva en servidor? Eso determina si cae
del lado barato y seguro de D11 o del caro y arriesgado. Si resultan ser casi todas
nativas, el módulo 3 es mucho menos arriesgado de lo que parecía.

### D22 — nombre del programa

Decidido que sea **marca nueva e independiente del canal** (más techo, sobrevive si el SaaS
crece solo). Falta elegirla. Debe encajar en el registro de DT-16: corta, concreta,
trading-native, y funcionar como paraguas sobre Calls · Journal · Desk · Club.

Antes de comprometerse con cualquiera: **verificar dominio, marca registrada y colisiones en
el sector cripto.** Ya se descartó `Openbook` por chocar con el DEX de Solana del mismo
nombre.

### D29 — qué fallo de performance se ataca primero

Las herramientas existen para corregir fallos concretos de tu operativa, no para completar
un terminal. Doc 35 §4 las agrupa en cinco categorías: calidad de ejecución,
dimensionamiento, disciplina, carga cognitiva y atención/latencia.

**Las cuatro primeras no requieren lógica viva en servidor. La quinta sí.** Por eso esta
decisión arrastra a D11: si tus herramientas atacan las cuatro primeras —lo típico en un
operador discrecional— el módulo 3 se construye con keys en cliente, sin custodia y sin
responsabilidad de ejecución.

**Enumerar tus herramientas por el fallo que corrigen, antes de diseñar nada.**

### D24 — umbral de calificación por volumen

La decisión más delicada del módulo 5, y no por razones técnicas. **Si el acceso depende del
volumen operado, le das a tu comunidad un incentivo directo para operar de más** — malo para
ellos y, a la larga, malo para tu credibilidad, que es el activo del que cuelga todo (PG1).

**Recomendación: calificar por tener cuenta activa bajo tu referido con un umbral mínimo y
simbólico, y nunca escalar beneficios con el volumen.** El ingreso de afiliado viene del
agregado de muchos usuarios, no de exprimir a unos pocos, así que un umbral bajo te da casi
lo mismo sin empujar a nadie.

### D25 — automatización de TradingView

No hay API oficial para gestionar acceso a scripts *invite-only*. La vía automatizada
conocida usa endpoints no soportados y **exige desactivar el 2FA de tu cuenta de vendedor**,
además de guardar sus credenciales en tu servidor.

**Recomendación: semi-automático.** El sistema calcula quién debería tener acceso y produce
la cola de trabajo con el diff; tú haces los clics. Automatizas lo difícil —saber quién
califica, que hoy nadie sabe— y no automatizas lo peligroso. Coste: un par de minutos por
semana.

## 5. Decisiones que ya no se tomarán

Registradas para no reabrir discusiones cerradas.

| Descartado | Por qué |
|---|---|
| Discord como frontend principal | DT-1 |
| Portfolio público (con o sin montos) | Expone patrimonio, agrava el perfil regulatorio y exige verificación on-chain para valer algo. Sustituido por el feed de trades del módulo 3a, que sí es verificable |
| Leer el portfolio desde la API de CoinGecko | **No existe ese endpoint.** La API sirve datos de mercado, no holdings de usuario (doc 90 §2) |
| Alpha Vantage como proveedor de datos | 25 llamadas/día en el plan gratuito. Inservible (doc 90 §3) |
| Estado "Superseded" para proyecciones | Sería la puerta trasera para retirar las perdedoras antes de que fallen (doc 10 §4.4) |
| Construir copy trading propio | DT-10. Actividad regulada en la mayoría de jurisdicciones; los exchanges ya lo ofrecen y son la entidad regulada |
| Automatizar TradingView por completo | Exige desactivar el 2FA de la cuenta que contiene tu propiedad intelectual, sobre endpoints no soportados (D25) |
| El terminal justificado por ahorrar suscripciones | Una suscripción cuesta mucho menos que los meses de ingeniería. Lo que lo justifica son las herramientas propias (doc 35 §1) |
