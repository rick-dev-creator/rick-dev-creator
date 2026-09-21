# Módulo 4 — Copy Trading (futuro)

> Evaluación estratégica. **Este módulo no es una escalada de tamaño: es un cambio de
> naturaleza legal del proyecto.** Léelo antes de invertir tiempo de ingeniería en él.

---

## 1. La línea que cruza el módulo 4

Los módulos 1 a 3 son, legalmente, cosas relativamente mansas:

| Módulo | Qué haces | Naturaleza |
|---|---|---|
| 1 — Proyecciones | Publicas análisis | Publicación de contenido |
| 2 — Ledger | Registras tus posiciones en privado | Uso personal |
| 3 — Terminal | Das una herramienta; el usuario decide y ejecuta | Software |
| **4 — Copy trading** | **Tus decisiones ejecutan en cuentas ajenas** | **Gestión discrecional** |

En la mayoría de jurisdicciones, **tomar decisiones de inversión que se ejecutan en la
cuenta de un tercero, a cambio de una compensación, es actividad regulada** — gestión de
carteras o asesoría de inversión, según el país. No es una zona gris creada por lo novedoso
de crypto: los reguladores ya se pronunciaron específicamente sobre copy trading.

- **ESMA** publicó en 2023 un *supervisory briefing* dedicado a copy trading.
- **IOSCO** siguió en 2024 con guía sobre *online imitative trading*.

Ambos se centran exactamente en el riesgo de que alguien siga automáticamente las
posiciones de otro.

Y tu configuración agrava el análisis: **cobras un porcentaje de los fees que genera el
volumen de esos usuarios.** Es decir, ganas más cuanto más operen las cuentas que tú mismo
estás dirigiendo. Esa combinación —dirigir la operación y cobrar por su volumen— es
precisamente el patrón que los reguladores buscan.

No es un "ten cuidado". Es un **requiere abogado en tu jurisdicción antes de escribir una
línea de código**.

---

## 2. La alternativa que probablemente elimina el módulo entero

**Los exchanges ya ofrecen copy trading nativo, y ya tienes partnership con ellos.**

Binance, OKX, Bybit, Bitget, BingX y MEXC tienen programas maduros de copy trading donde
un trader se registra como *master trader* y otros lo copian dentro de la plataforma.

Lo que eso te da:

| | Construirlo tú | Master trader en el exchange |
|---|---|---|
| Entidad regulada | **Tú** | El exchange |
| Custodia de keys | Tuya | Ninguna |
| Responsabilidad de ejecución | Tuya | Del exchange |
| Reparto de ganancias | Lo construyes | Incluido |
| Coste de ingeniería | Enorme | **Cero** |
| Tiempo al mercado | Años | Días |
| Alineado con tu afiliación | Sí | **Sí, y refuerza el referido** |

Además encaja perfecto con tu objetivo: la gente que te copia **tiene que abrir cuenta en
ese exchange**, que es exactamente lo que tu modelo de afiliado necesita. El copy trading
nativo es un motor de referidos, no un competidor de tu terminal.

**Recomendación: no construyas copy trading. Vuélvete master trader en los exchanges donde
ya tienes partnership.** Obtienes el 90% del beneficio con el 0% del riesgo legal y de
ingeniería. Si el copy trading nativo resulta insuficiente después de usarlo en serio,
entonces —y solo entonces— evalúa construir algo, ya con datos reales y con abogado.

---

## 3. Lo que tu terminal sí puede aportar, sin cruzar la línea

Hay una diferencia legal sustantiva entre **señal** y **ejecución**:

- **Señal (no discrecional):** publicas lo que hiciste; el usuario decide si actúa y ejecuta
  él mismo. Está mucho más cerca del módulo 1 — es publicación.
- **Ejecución (discrecional):** tu decisión toca su cuenta automáticamente. Actividad
  regulada.

Tu terminal puede ofrecer legítimamente un **feed de operaciones en vivo**: cuando abres o
cierras una posición, la plataforma lo publica y notifica. El usuario lo ve y decide. Un
botón de "pre-cargar esta orden en mi terminal" **que el usuario tiene que confirmar y
enviar** sigue del lado de la señal, no de la ejecución.

Eso cubre gran parte del valor percibido de copy trading —"quiero ver y seguir lo que hace
Rick"— sin convertirte en gestor de carteras. Y se apoya directamente en el módulo 3a
(§6 del documento 04), que ya lee tus posiciones reales de los exchanges.

**El matiz que no puedes ignorar:** cuanto más automático y menos deliberado sea ese paso
de confirmación, más se parece a ejecución a ojos de un regulador. Un botón que el usuario
pulsa conscientemente cada vez es una cosa; un "auto-confirmar todo" es copy trading con
otro nombre. La fricción ahí es una característica de cumplimiento, no un defecto de UX.

---

## 4. Riesgos adicionales si aun así lo construyes

- **Front-running inverso.** Si tu audiencia copia tus entradas, mueve el precio en tu
  contra en activos poco líquidos. Cuanto mejor funcione, peor ejecuta todo el mundo,
  incluido tú.
- **Responsabilidad por pérdidas.** Cuando el copy pierda dinero —y va a perder— el
  reclamo llega a ti, no al mercado. Con custodia de keys y ejecución automática, tu
  posición es indefendible sin licencia.
- **Riesgo de concentración.** Miles de cuentas entrando al mismo activo al mismo tiempo
  desde tu sistema es un patrón que exchanges y reguladores detectan.
- **Escalado operativo.** Replicar una orden en N cuentas con tamaños proporcionales,
  slippage distinto y fallos parciales es un problema de sistemas distribuidos serio —
  y cada fallo parcial es dinero real de otra persona.

---

## 5. Decisiones abiertas

| # | Decisión | Impacto |
|---|---|---|
| D17 | ¿Master trader nativo o construcción propia? | **Máximo** — recomendación fuerte: nativo |
| D18 | Jurisdicción y asesoría legal | **Bloqueante** para cualquier opción propia |
| D19 | ¿Feed de señales en el terminal (§3)? | Alto — es el punto medio con mejor relación valor/riesgo |
| D20 | Divulgación del reparto de fees | Alto — obligatoria en cualquier escenario |

---

## Fuentes

- [ESMA — Supervisory briefing on copy trading (2023)](https://cryptoslate.com/crypto-exchanges/copy-trading/)
- [Comparativa de plataformas de copy trading nativo](https://www.thestreet.com/crypto/newsroom/best-copy-trading-platforms-for-2026)
- [Bitget Copy Trading — revisión](https://coinbureau.com/review/bitget-copy-trading-review)
