# Visión y Roadmap

> Documento raíz del programa. Índice completo en [README.md](./README.md);
> vocabulario en [01-convenciones.md](./01-convenciones.md); decisiones en
> [02-decisiones.md](./02-decisiones.md).

| Doc | Contenido |
|---|---|
| [10](./10-modulo-1-proyecciones.md) | Módulo 1 — Proyecciones (público, inmutable) |
| [20](./20-modulo-2-ledger.md) | Módulo 2 — Ledger de posiciones (privado) |
| [30](./30-modulo-3-terminal.md) | Módulo 3 — Terminal multi-exchange |
| [40](./40-modulo-4-copy-trading.md) | Módulo 4 — Copy trading (futuro) |
| [90](./90-fuentes-de-datos.md) | Fuentes de datos de mercado (transversal) |

---

## 1. Esto no son cuatro apps: es un embudo

El objetivo declarado es que la comunidad vea el valor de la plataforma, vea cómo inviertes
y operas, use tus exchanges y use las herramientas. Eso no describe cuatro productos
separados. Describe **un solo embudo con cuatro etapas**, donde cada una alimenta la
siguiente:

```
  Módulo 1          →   Feed de trades   →     Módulo 3        →   Fees de
  Proyecciones          Ver cómo operas        Terminal            afiliado
  públicas              en vivo                gratis operando
  ───────────           ───────────            ───────────         ───────────
  CREA CONFIANZA        CREA DESEO             CREA RETENCIÓN      MONETIZA
  "acierta y            "quiero seguir         "vivo aquí          "opero bajo
   puedo verificarlo"    lo que hace"           todos los días"     su referido"

                         Módulo 2 (privado) alimenta al 1: mejora tu TA
                         Suscripción: monetiza a quien no opera bajo tu referido
```

Dos consecuencias que conviene tener presentes:

1. **El módulo 1 es el cimiento, no el calentamiento.** Sin credibilidad verificable, el
   resto del embudo no tiene de dónde agarrarse. Nadie usa el terminal de alguien en quien
   no confía, y nadie abre cuenta bajo el referido de alguien que no ha demostrado nada.
2. **El ingreso principal no es la suscripción, son los fees.** El acceso gratis mientras
   operen bajo tu referido no es una concesión: es el motor. Cada usuario "gratis" es un
   usuario que genera ingreso. La suscripción solo captura al que no quiere cambiar de
   exchange.

---

## 2. Una tensión que hay que resolver explícitamente

El objetivo dice **"donde posteo mis trades, vean cómo invierto y tradeo"**. El módulo 2 se
definió como **privado**. Parece contradicción, pero no lo es — son dos cosas distintas que
conviene no confundir:

| | Portfolio | Feed de trades |
|---|---|---|
| Revela | Patrimonio, asignación, cuánto tienes | Qué operaste, cuándo, a qué precio |
| Riesgo personal | **Alto** (exposición patrimonial) | **Bajo** si se omiten montos |
| Riesgo regulatorio | Medio | Bajo (es publicación, no ejecución) |
| Valor para la comunidad | Bajo — "cuánto tiene" no enseña nada | **Alto** — "cómo opera" es lo que quieren ver |

Lo que tu comunidad quiere no es saber cuánto dinero tienes. Es **ver cómo operas**:
entradas, salidas, gestión del stop, cuándo te sales antes de tiempo, cuándo aguantas.

> "Entré ETH largo en 3,200, stop en 3,050, 6% del portfolio" enseña todo.
> "Tengo $340,000 en ETH" no enseña nada y te pone en riesgo.

**Resolución: el ledger del módulo 2 se queda privado. El feed público de trades es una cosa
aparte** — sin montos absolutos, con tamaño en % — y **no sale del módulo 2: sale del
módulo 3a.**

Eso último importa mucho. Un feed alimentado por la lectura directa de tus exchanges es
**verificado automáticamente**, no declarado a mano. Es la misma diferencia de credibilidad
que el módulo 1 introduce para el análisis, aplicada ahora a tus operaciones — y resuelve
de raíz el problema que hizo descartar un portfolio público manual.

---

## 3. Secuencia recomendada

El orden está elegido para que cada etapa sea útil por sí sola y reduzca el riesgo de la
siguiente.

### Etapa 1 — Módulo 1 en producción `← empezar aquí`
Proyecciones públicas, motor de resolución, scorecard, modo stream, bot notificador.
Validado con crypto primero (doc 90 §5).
**Criterio de salida:** lo usas en tus lives y el récord crece solo cada semana.

### Etapa 2 — Módulo 2 privado
Ledger acotado y, sobre todo, el informe de coherencia. Barato porque no añade
dependencias de datos.
**Criterio de salida:** puedes responder "¿respeté mi invalidación?" con un número.

### Etapa 3 — Módulo 3a: agregación de solo lectura
Tus cuentas, todos los exchanges, una pantalla. Sin ejecutar órdenes.
Valida lo difícil (conectividad, normalización, reconciliación) sin riesgo, **y habilita
el feed público de trades de §2**, que es lo que el objetivo pide.
**Criterio de salida:** el feed lleva semanas publicando tus operaciones sin errores.

### Etapa 4 — Módulo 3b: ejecución, solo para ti
Órdenes, SL/TP visual. Un usuario: tú, con tu dinero.
**Criterio de salida:** meses operando en serio sin un solo fallo de ejecución.

### Etapa 5 — Módulo 3c: SaaS multi-usuario
Keys de terceros, afiliación, suscripciones, soporte, on-call. **Aquí el proyecto se
convierte en empresa.** Requiere asesoría legal previa.

### Módulo 4 — probablemente no se construye
Hazte *master trader* en el copy trading nativo de los exchanges con los que ya tienes
partnership. Mismo beneficio, cero riesgo legal, cero ingeniería (doc 40 §2).

---

## 4. La pregunta abierta más importante

Mencionas que para el terminal quieres **"implementar herramientas que no he visto en otras
apps"**. Eso, y no el terminal, es la tesis de diferenciación del módulo 3 — y hoy no está
definida en ningún lado.

Importa más de lo que parece, porque **puede cambiar la secuencia entera**. Si esas
herramientas son el motivo real por el que alguien vendría, entonces:

- El MVP del módulo 3 **no es un terminal completo**, es *esa* herramienta.
- Una herramienta única que nadie más tiene es mejor gancho de adquisición que un terminal
  mediocre que compite con productos maduros y con años de ventaja.
- Incluso podría vivir **antes** que la ejecución, como función independiente sobre la
  agregación de solo lectura de la etapa 3 — sin tocar órdenes ni asumir riesgo.

**Siguiente paso concreto: nómbralas y descríbelas.** Merecen su propio documento.

---

## 5. Los tres riesgos de todo el programa

1. **Que el módulo 3 devore al 1 y al 2.** Es el riesgo más probable: el terminal es mucho
   más divertido de construir. Pero sin el módulo 1 no hay confianza, y sin confianza el
   terminal no tiene a quién servir. Regla sugerida: **no tocar el módulo 3 hasta que el 1
   esté en producción y te esté sirviendo en tus lives.**
2. **El conflicto de interés.** Ganas por volumen, no por el rendimiento de tu comunidad.
   Publicar proyecciones + cobrar fees de quien las sigue es una combinación visible y va a
   ser señalada. La respuesta correcta es divulgarlo de frente, siempre, en el sitio y en
   los videos. Declararlo tú es infinitamente mejor que te lo descubran.
3. **La escalada regulatoria y de custodia.** Cada módulo sube el nivel: publicar (1) →
   privado (2) → software (3a-3b) → custodia de credenciales ajenas (3c) → gestión
   discrecional (4). Los dos últimos saltos necesitan abogado **antes**, no después.

---

## 6. Qué hacer esta semana

1. Cerrar las decisiones abiertas del módulo 1: D1 (proveedor de datos), D3 (histórico
   retroactivo), D4 (targets y hit parcial).
2. Hacer el ejercicio del doc 10 §13.1: **resolver a mano 10 proyecciones reales pasadas**
   con las reglas de resolución. Ahí salen todos los huecos del modelo, y salen gratis.
3. Escribir el documento de las herramientas diferenciadoras del §4.

Nada de esto requiere escribir código todavía, y las tres cosas reducen retrabajo después.
