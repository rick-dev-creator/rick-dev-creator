# Los módulos: función, objetivo y expectativa

> Capa de arriba. Una ficha por módulo: qué hace, para qué existe, qué se espera de él y
> dónde termina. El detalle vive en los documentos `10`–`50` y se discute después.

---

## Vista de conjunto

| # | Módulo | Función en una línea | Objetivo | Éxito se mide con |
|---|---|---|---|---|
| 1 | **Calls** | Registra y califica tus proyecciones públicas | Crear confianza verificable | Récord que crece solo y aguanta a un escéptico |
| 2 | **Journal** | Registra en privado lo que realmente operaste | Saber si haces lo que dices | "¿Respeté mi invalidación?" con un número |
| 3 | **Desk** | Opera todas tus cuentas desde una interfaz | Mejorar tu performance | Que el módulo 2 muestre la mejora |
| 4 | — | Copy trading | — | **No se construye** |
| 5 | **Club** | Decide quién accede a qué y por qué vía | Migrar de Patreon a fees | Ningún acceso sin derecho vigente |

Los tres primeros forman un circuito: **Calls dice lo que harías, Desk hace, Journal juzga.**
Club es la frontera comercial que rodea a los tres.

---

## 1 · Calls

**Función** — Capturar cada proyección pública de análisis técnico en una forma falsable, y
resolverla automáticamente contra datos de mercado usando reglas fijas y publicadas.

**Objetivo** — Convertir tu análisis en un **récord que un tercero puede auditar sin
confiar en ti**. Es el cimiento del embudo entero: nadie usa las herramientas ni abre cuenta
bajo el referido de alguien que no ha demostrado nada.

**Qué se espera de él**

- Capturar una proyección en **menos de 2 minutos**, o el hábito muere.
- **Más del 90% se resuelven solas**, sin que intervengas.
- Presentable **en vivo sin edición previa**.
- Un escéptico puede rastrear cualquier proyección hasta el minuto exacto del video.
- El récord **crece solo cada semana** una vez capturas.

**Dónde termina** — No da señales, no ejecuta órdenes, no gestiona portafolio. No opina
sobre si deberías operar algo: solo registra lo que dijiste y si ocurrió.

**Relación** — No depende de ningún otro módulo. Los habilita a todos.

---

## 2 · Journal

**Función** — Registrar en privado tus posiciones reales y cruzarlas con las proyecciones de
Calls.

**Objetivo** — Responder una pregunta que ninguna app de portfolio puede responder porque no
conoce tus proyecciones: **¿hiciste lo que dijiste?** Y separar dos problemas que se
confunden todo el tiempo: *"mi análisis es malo"* frente a *"mi análisis es bueno y mi
ejecución es mala"*. Son diagnósticos distintos con soluciones distintas.

**Qué se espera de él**

- Responder con un número **si respetaste tu propia invalidación**.
- Decir en qué porcentaje de tus proyecciones realmente tomaste posición, y si dimensionas
  según tu convicción declarada.
- **Avisar cuando el precio rompió la invalidación y la posición sigue abierta.** Eso solo
  ya justifica el módulo.
- Seguir siendo barato: no añade dependencias de datos nuevas.

**Dónde termina** — No es un portfolio tracker. Sin cost basis, sin tax lots, sin
transferencias, sin fees, sin reportes fiscales, sin vista pública. Si empieza a parecerse a
CoinStats, se salió de su sitio.

**Relación** — Consume contratos de Calls. Es quien mide si Desk sirvió de algo.

---

## 3 · Desk

**Función** — Operar todas tus cuentas de exchange desde una sola interfaz, con herramientas
que corrigen fallos concretos de tu operativa y que los exchanges no ofrecen al nivel que
necesitas.

**Objetivo** — **Mejorar tu performance**, en trading y en investing. Es un instrumento
personal primero. Que además retenga miembros y genere fees de afiliado es consecuencia, no
causa.

**Qué se espera de él**

- Reflejar tus posiciones **sin errores**: una posición mostrada mal en un terminal es peor
  que no mostrarla.
- Al menos **una herramienta que no exista en otro lado** y que uses todos los días.
- Que **el Journal muestre la mejora**. Si no la muestra, la herramienta no sirvió.
- Funcionar con tus propias cuentas durante meses antes de tocar las de nadie más.

**Dónde termina** — No busca paridad de funciones con terminales maduros: pierde esa pelea y
no la necesita. **Nunca custodia fondos** ni pide permisos de retiro. Los stops viven en el
exchange, no en tu servidor.

**Relación** — Consume Calls (precargar escaleras desde una proyección) y los accesos de
Club. Alimenta a Journal automáticamente y publica los avisos en tiempo real.

---

## 4 · Copy trading

**Función** — Que otros repliquen tus operaciones automáticamente.

**Objetivo** — Monetizar tu operativa a escala.

**Qué se espera de él** — **Nada: no se construye.** Se delega al copy trading nativo de los
exchanges con los que ya tienes partnership, donde el exchange es la entidad regulada y
maneja ejecución y reparto.

**Por qué** — Ejecutar decisiones tuyas en cuentas ajenas a cambio de compensación es
actividad regulada en la mayoría de jurisdicciones. Construirlo te convertiría en gestor de
carteras; ser *master trader* te da casi el mismo beneficio con cero riesgo legal y cero
ingeniería — y refuerza tu programa de referidos, porque quien te copia debe abrir cuenta
ahí.

---

## 5 · Club

**Función** — Decidir quién tiene derecho a qué, por qué vía, y aplicarlo: roles de Discord,
acceso a indicadores, acceso a Desk.

**Objetivo** — **Mover el ingreso de Patreon a fees de afiliado**, que es donde ganas mucho
más, y dejar de perder dinero por accesos que nadie revoca.

**Qué se espera de él**

- **Ningún rol de Discord existe sin un derecho vigente que lo respalde.** Hoy esa fuga
  existe y nadie la mira.
- Un miembro nuevo desbloquea acceso **operando bajo tu referido, sin que toques nada**.
- La cola de invitaciones de TradingView se calcula sola; tú solo haces los clics.
- Todo derecho tiene fuente, evidencia y caducidad.

**Dónde termina** — No es un CRM ni una pasarela de pagos propia. No automatiza TradingView
por completo: eso exigiría desactivar tu 2FA sobre endpoints no soportados.

**Relación** — No depende de ningún módulo. Publica eventos que los demás escuchan.

---

## Módulo técnico · MarketData

No es un módulo de producto y no tiene objetivo de negocio, pero existe: **sirve precios y
velas** a Calls y a Journal, con dos capacidades separadas porque tienen costes muy
distintos —precio actual (gratis en las tres clases de activo) y OHLC intradía (lo único que
cuesta dinero)—. Guarda todo lo que consulta, para que el récord pueda reevaluarse con los
mismos datos originales.

---

## Lo que no se debe perder de vista

1. **Calls es el cimiento, no el calentamiento.** Sin él, ningún otro módulo tiene sobre qué
   apoyarse.
2. **Desk es el que se come el proyecto si lo dejas.** Es el más divertido de construir y el
   que menos ingresa por sí solo.
3. **Club es donde está el dinero más cercano.** No crea audiencia nueva: convierte la que
   ya tienes a un canal mejor.
4. **Journal parece el prescindible y no lo es.** Es lo único que convierte a Desk en una
   inversión medible en vez de un capricho.
