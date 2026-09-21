# Convenciones y principios transversales

> Define el vocabulario del programa y los principios que aplican a **todos** los módulos.
> Los principios propios de un módulo viven en su documento.

---

## 1. Vocabulario

Estas palabras se usaron de forma inconsistente mientras el programa crecía. A partir de
aquí significan exactamente esto y nada más.

| Término | Significado | Ejemplo |
|---|---|---|
| **Programa** | El conjunto completo de los cinco módulos | — |
| **Módulo** | Un área funcional. Son **cinco** y no cambian de número | Módulo 1 = Proyecciones |
| **Etapa** | Un incremento entregable del roadmap. Son **siete** y definen el orden de trabajo. **Se renumeran si el roadmap cambia**, a diferencia de los módulos | Etapa 5 = Módulo 3a |
| ~~Fase~~ | **No usar.** Ambiguo entre módulo y etapa | — |

### Nombres de los módulos

El **número es el identificador estable**; el nombre es la etiqueta humana y de marca. Los
dos se usan indistintamente y `Módulo 1` nunca deja de ser `Calls`.

| # | Nombre | Namespace | Qué es | Público |
|---|---|---|---|---|
| 1 | **Calls** | `Calls` | Récord verificable de proyecciones | Sí, y gratis |
| 2 | **Journal** | `Journal` | Registro privado de posiciones | No, solo tú |
| 3 | **Desk** | `Desk` | Terminal multi-exchange | Sí, miembros |
| 4 | — | — | Copy trading | **No se construye** |
| 5 | **Club** | `Club` | Derechos y accesos de membresía | Interno |

Convención de idioma (DT-15): **todo en inglés**, código e interfaz pública. El módulo 4 no
recibe nombre porque no se construye.

Un módulo puede abarcar varias etapas: el módulo 3 se entrega en las etapas 5, 6 y 7
(3a lectura, 3b ejecución propia, 3c SaaS) y el módulo 5 en las etapas 2 y 3.
Un módulo dice *qué*, una etapa dice *cuándo*.

### Términos de dominio

| Término | Significado |
|---|---|
| **Actividad** | **Trading** o **Investing**. Dimensión transversal a los módulos 1, 2 y 3: separa dos disciplinas con horizontes y métricas incomparables |
| **Proyección** | Afirmación pública y falsable sobre un activo. Inmutable al publicarse |
| **Posición** | Operación real tuya en un exchange. Privada, editable |
| **Invalidación** | Precio que, al romperse, mata la tesis de una proyección |
| **Horizonte** | Fecha límite dura de una proyección. Al vencer, expira |
| **R** | Unidad de riesgo: `(target − referencia) / (referencia − invalidación)` |
| **Resolución** | Veredicto del motor sobre una proyección: Hit, Parcial, Invalidada o Expirada |
| **Feed de trades** | Tus operaciones **sin montos**, para miembros. Sale del módulo 3a, no del 2 |
| **Journal** | El registro privado de posiciones del módulo 2. No es un portfolio tracker |
| **Derecho** | Permiso con fuente, evidencia y caducidad que habilita accesos. Nunca un rol suelto |
| **Tier** | Nivel de membresía. Se **deriva** de los derechos vigentes, nunca se asigna a mano |
| **Escalera** | Conjunto de órdenes límite distribuidas en un rango, gestionado como una unidad |

### Distinción que conviene no perder

**Portfolio ≠ feed de trades.** El portfolio revela cuánto tienes: privado siempre. El feed
revela cómo operas: sin montos y reservado a miembros (PG6). Confundirlos fue la
contradicción que se resolvió en [00-vision-y-roadmap.md](./00-vision-y-roadmap.md) §2.

---

## 2. Principios del programa

Aplican a los cinco módulos. Un módulo puede añadir los suyos, nunca contradecir estos.

### PG1 — La credibilidad es el activo

Todo el embudo descansa en que la gente crea lo que dices. Cualquier funcionalidad que
permita maquillar el récord —editar una proyección publicada, retirar una perdedora,
excluir las expiradas de las métricas— destruye más valor del que aporta. Ante la duda, la
opción que más te expone es la correcta.

### PG2 — Cada etapa es útil por sí sola

Ninguna etapa se justifica solo como cimiento de la siguiente. Si la etapa 5 (agregación de
solo lectura) no te sirve aunque nunca llegue la 6, está mal definida. Esto es lo que
permite parar el programa en cualquier punto sin haber desperdiciado el trabajo.

### PG3 — Divulgación permanente del conflicto de interés

Cobras por volumen de trading, no por el rendimiento de tu comunidad. Ese conflicto se
declara de frente en el sitio y en los videos, siempre. Declararlo tú es infinitamente
mejor que te lo descubran.

### PG4 — Escalada de riesgo consciente

Cada módulo sube el nivel de exposición legal y operativa:

```
Módulos 1, 2 y 5   Módulo 3a-3b   Módulo 3c          Módulo 4
publicar, uso   →  software    →  custodia de    →   gestión
propio, accesos    propio         keys ajenas        discrecional
──────────────────────────────────────────────────────────────────────
      riesgo bajo          │   requiere abogado ANTES, no después
```

Cruzar cualquiera de los dos últimos saltos sin asesoría legal previa no es una decisión
técnica y no se toma en un documento de ingeniería.

### PG5 — Nunca custodia de fondos

El programa jamás mantiene fondos de terceros ni solicita permisos de retiro en ninguna API
key. Las claves de exchange se piden siempre con permisos mínimos y, donde exista,
whitelist de IP. Este principio no tiene excepciones ni casos especiales.

### PG7 — Trading e investing nunca se mezclan en una métrica

Son dos disciplinas distintas, con horizontes, R y tasas de acierto que no se comparan. Toda
métrica, scorecard e informe se segmenta por actividad antes de agregarse. Un número que las
mezcla no es una simplificación: es un número falso.

### PG6 — La prueba es pública, la oportunidad es de pago

Lo que demuestra credibilidad —las proyecciones y su récord resuelto— es público y gratuito
siempre. Lo que tiene valor por ser oportuno —avisos en tiempo real, canales privados,
indicadores, terminal— es de miembros. Cobrar por el récord mataría el embudo, porque el
récord es lo que vuelve creíble todo lo demás.

---

## 3. Convenciones de los documentos

**Numeración con huecos.** `00-09` transversales, una decena por módulo (`10`, `20`, `30`,
`40`, `50`), los intermedios para anexos de ese módulo (`35` = herramientas del módulo 3), y
`90+` para anexos del programa. Los huecos permiten insertar sin renumerar.

**Referencias.** Entre documentos, por número (`doc 30 §6`). Dentro de un documento, por
sección (`§5.4`). Los números de módulo y de decisión **son estables**: no se reutilizan ni
se reordenan aunque cambie el roadmap.

**Decisiones.** Viven **solo** en [02-decisiones.md](./02-decisiones.md). Los documentos de
módulo enlazan al registro, nunca copian la tabla.

**Estado.** Todos los documentos son conceptuales: describen *qué* y *por qué*, no *cómo*.
Las decisiones técnicas se registran como restricciones, no se desarrollan. Cuando empiece
la implementación, los documentos de arquitectura serán nuevos, no ediciones de éstos.
