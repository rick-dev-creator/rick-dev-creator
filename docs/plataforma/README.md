# Plataforma de trading — documentación conceptual

Planeación de una plataforma en cinco módulos que convierte la audiencia del canal de
YouTube en una comunidad que verifica tu análisis, ve cómo operas y usa tus herramientas.

**Estado:** conceptual. Nada implementado todavía. Ninguna decisión técnica desarrollada.

---

## Orden de lectura

| # | Documento | Qué contiene |
|---|---|---|
| **00** | [Visión y roadmap](./00-vision-y-roadmap.md) | **Empieza aquí.** El embudo, la secuencia en siete etapas y los riesgos del programa |
| 01 | [Convenciones](./01-convenciones.md) | Vocabulario y principios transversales |
| 02 | [Decisiones](./02-decisiones.md) | Registro único: lo decidido, lo abierto y lo descartado |
| 10 | [Módulo 1 — Proyecciones](./10-modulo-1-proyecciones.md) | Récord público, inmutable y auditable de tu análisis técnico |
| 20 | [Módulo 2 — Ledger](./20-modulo-2-ledger.md) | Registro privado de posiciones para auto-diagnóstico |
| 30 | [Módulo 3 — Terminal](./30-modulo-3-terminal.md) | Terminal multi-exchange y el SaaS |
| 35 | [Herramientas diferenciadoras](./35-herramientas-diferenciadoras.md) | La tesis de valor del terminal. Documento abierto |
| 40 | [Módulo 4 — Copy trading](./40-modulo-4-copy-trading.md) | Evaluación legal y por qué no se construye |
| 50 | [Módulo 5 — Membresías](./50-modulo-5-membresias.md) | Derechos, roles de Discord y migración de Patreon a fees |
| 90 | [Fuentes de datos](./90-fuentes-de-datos.md) | Proveedores de precios y sus límites, para los módulos 1 y 2 |

Si solo vas a leer dos: **00** y **02**.

---

## Los cinco módulos en una línea

| Módulo | Qué es | Visible | Estado |
|---|---|---|---|
| 1 — Proyecciones | Récord verificable de tu TA | **Público** | Listo para construirse |
| 2 — Ledger | "¿Respeté mi propia invalidación?" | Privado | Conceptual |
| 3 — Terminal | Herramienta multi-exchange y SaaS | Mixto | Evaluación estratégica |
| 4 — Copy trading | Que otros repliquen tus operaciones | Público | **Decidido: no se construye** |
| 5 — Membresías | Quién accede a qué, y por qué vía | Interno | Conceptual |

---

## Las tres cosas que conviene no olvidar

1. **El módulo 1 es el cimiento, no el calentamiento.** Sin credibilidad verificable el
   resto del embudo no tiene de dónde agarrarse: nadie usa el terminal de alguien en quien
   no confía, ni abre cuenta bajo su referido.
2. **El ingreso principal no es la suscripción, son los fees de afiliado.** El terminal es
   el vehículo, no el producto. Eso cambia por completo su alcance mínimo.
3. **El riesgo número uno es que el módulo 3 se coma al 1 y al 2.** Es el más divertido de
   construir y por eso es el que se lleva el tiempo. Regla: no tocarlo hasta que el módulo
   1 esté en producción.

---

## Siguiente paso

Cerrar las decisiones de [02-decisiones.md §3.1](./02-decisiones.md) y hacer el ejercicio
del [doc 10 §13.1](./10-modulo-1-proyecciones.md): resolver a mano 10 proyecciones reales
pasadas con las reglas de resolución. Ahí salen los huecos del modelo, y salen gratis.
