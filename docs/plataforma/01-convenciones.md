# Convenciones y principios transversales

> Define el vocabulario del programa y los principios que aplican a **todos** los módulos.
> Los principios propios de un módulo viven en su documento.

---

## 1. Vocabulario

Estas palabras se usaron de forma inconsistente mientras el programa crecía. A partir de
aquí significan exactamente esto y nada más.

| Término | Significado | Ejemplo |
|---|---|---|
| **Programa** | El conjunto completo de los cuatro módulos | — |
| **Módulo** | Un área funcional. Son **cuatro** y no cambian de número | Módulo 1 = Proyecciones |
| **Etapa** | Un incremento entregable del roadmap. Son **cinco** y definen el orden de trabajo | Etapa 3 = Módulo 3a |
| ~~Fase~~ | **No usar.** Ambiguo entre módulo y etapa | — |

Un módulo puede abarcar varias etapas: el módulo 3 se entrega en las etapas 3, 4 y 5
(3a lectura, 3b ejecución propia, 3c SaaS). Un módulo dice *qué*, una etapa dice *cuándo*.

### Términos de dominio

| Término | Significado |
|---|---|
| **Proyección** | Afirmación pública y falsable sobre un activo. Inmutable al publicarse |
| **Posición** | Operación real tuya en un exchange. Privada, editable |
| **Invalidación** | Precio que, al romperse, mata la tesis de una proyección |
| **Horizonte** | Fecha límite dura de una proyección. Al vencer, expira |
| **R** | Unidad de riesgo: `(target − referencia) / (referencia − invalidación)` |
| **Resolución** | Veredicto del motor sobre una proyección: Hit, Parcial, Invalidada o Expirada |
| **Feed de trades** | Publicación de tus operaciones **sin montos**. Sale del módulo 3a, no del 2 |
| **Ledger** | Registro privado de posiciones del módulo 2. No es un portfolio tracker |

### Distinción que conviene no perder

**Portfolio ≠ feed de trades.** El portfolio revela cuánto tienes (privado, siempre). El
feed revela cómo operas (público, sin montos). Confundirlos fue la contradicción que se
resolvió en [00-vision-y-roadmap.md](./00-vision-y-roadmap.md) §2.

---

## 2. Principios del programa

Aplican a los cuatro módulos. Un módulo puede añadir los suyos, nunca contradecir estos.

### PG1 — La credibilidad es el activo

Todo el embudo descansa en que la gente crea lo que dices. Cualquier funcionalidad que
permita maquillar el récord —editar una proyección publicada, retirar una perdedora,
excluir las expiradas de las métricas— destruye más valor del que aporta. Ante la duda, la
opción que más te expone es la correcta.

### PG2 — Cada etapa es útil por sí sola

Ninguna etapa se justifica solo como cimiento de la siguiente. Si la etapa 3 (agregación de
solo lectura) no te sirve aunque nunca llegue la 4, está mal definida. Esto es lo que
permite parar el programa en cualquier punto sin haber desperdiciado el trabajo.

### PG3 — Divulgación permanente del conflicto de interés

Cobras por volumen de trading, no por el rendimiento de tu comunidad. Ese conflicto se
declara de frente en el sitio y en los videos, siempre. Declararlo tú es infinitamente
mejor que te lo descubran.

### PG4 — Escalada de riesgo consciente

Cada módulo sube el nivel de exposición legal y operativa:

```
Módulo 1        Módulo 2       Módulo 3a-3b      Módulo 3c          Módulo 4
publicar   →    privado   →    software     →    custodia de   →   gestión
contenido       uso propio     propio            keys ajenas        discrecional
────────────────────────────────────────────────────────────────────────────────
        riesgo bajo              │        requiere abogado ANTES, no después
```

Cruzar cualquiera de los dos últimos saltos sin asesoría legal previa no es una decisión
técnica y no se toma en un documento de ingeniería.

### PG5 — Nunca custodia de fondos

El programa jamás mantiene fondos de terceros ni solicita permisos de retiro en ninguna API
key. Las claves de exchange se piden siempre con permisos mínimos y, donde exista,
whitelist de IP. Este principio no tiene excepciones ni casos especiales.

---

## 3. Convenciones de los documentos

**Numeración con huecos.** `00-09` transversales, `10/20/30/40` un módulo cada uno, `90+`
anexos. Los huecos permiten insertar sin renumerar.

**Referencias.** Entre documentos, por número (`doc 30 §6`). Dentro de un documento, por
sección (`§5.4`). Los números de módulo y de decisión **son estables**: no se reutilizan ni
se reordenan aunque cambie el roadmap.

**Decisiones.** Viven **solo** en [02-decisiones.md](./02-decisiones.md). Los documentos de
módulo enlazan al registro, nunca copian la tabla.

**Estado.** Todos los documentos son conceptuales: describen *qué* y *por qué*, no *cómo*.
Las decisiones técnicas se registran como restricciones, no se desarrollan. Cuando empiece
la implementación, los documentos de arquitectura serán nuevos, no ediciones de éstos.
