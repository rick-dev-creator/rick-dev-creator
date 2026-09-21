# Arquitectura — frontend

> Define stack, dirección visual y sistema de diseño. Sin código.
> Backend en [03-arquitectura.md](./03-arquitectura.md).

---

## 1. Un solo stack, dos perfiles de ruta

Calls y Desk tienen requisitos opuestos:

| | **Calls** (público) | **Desk** (terminal) |
|---|---|---|
| Audiencia | Cualquiera, mucha desde el móvil | Tú y miembros, en escritorio |
| Necesita | SEO, carga rápida, compartible, legible en stream | Densidad, canvas, atajos, websockets vivos |
| Renderizado | **Prerenderizado** | **Cliente, sin SSR** |
| Métrica que importa | Tiempo hasta el primer render | Latencia de interacción |

La versión anterior de este documento proponía **dos stacks** (Astro para Calls, React para
Desk) optimizando cada perfil por separado. **Se descarta: un solo stack.**

Y es la decisión correcta para el tamaño de este equipo. Dos stacks significan dos builds,
dos conjuntos de dependencias que actualizar, dos modelos mentales, y una librería de
componentes que en la práctica **no se comparte** —solo se comparten los tokens—. Para un
equipo pequeño eso se paga cada semana, mientras que el beneficio (unos kilobytes menos en
la página pública) se cobra una sola vez.

Lo que sí cuesta, dicho sin adornos: **Calls enviará más JavaScript del que enviaría con
Astro** —del orden de 9 KB frente a más de 100—. Se mitiga con prerenderizado y división de
código por ruta, pero no desaparece. Es el precio del stack único, y es asumible.

---

## 2. El stack — revisa D21

### **TanStack Start v1** para todo

Estable desde marzo de 2026, sobre Vite y React. Cubre los dos perfiles **en el mismo
proyecto**, que es justo lo que hace viable el stack único:

- **Rutas de Calls → prerenderizadas.** Generación estática en build, con rastreo de enlaces
  para cubrir el histórico de proyecciones. HTML plano, indexable, rápido.
- **Rutas de Desk → `ssr: false`.** Sin ejecución en servidor, sin SSR del componente:
  cliente puro, que es lo que necesita un terminal con websockets.

El **SSR selectivo por ruta** es una función de primera clase, no un apaño: se configura con
la propiedad `ssr` por ruta y un `defaultSsr` global. Es literalmente el caso de uso de dos
aplicaciones muy distintas bajo un mismo techo.

### Por qué esta y no Next.js

Next también sabe hacer ambas cosas. La diferencia está en **hacia dónde te empuja**.

En Next con App Router, el paradigma por defecto son los server components y las server
actions. Con un backend .NET detrás, eso es exactamente lo que no quieres: **un segundo
sitio donde acaba viviendo lógica de negocio**, contra DT-17 y las reglas de independencia
de módulos. No es que no puedas evitarlo — es que nadas contra la corriente del framework
todos los días.

En TanStack Start las server functions son **opt-in explícito**. No usarlas no es luchar
contra nada.

Además: Vite como base, y **search params tipados y validados por esquema**, que en Desk
convierten el estado de la pantalla en URL compartible y restaurable —
`?symbol=BTCUSDT&layout=ladder&tf=4h`. Cada disposición de paneles es un enlace, lo que
encaja con el tiling y con el diseño conducido por teclado.

### La regla que no se rompe

> **No se usan server functions. La API de .NET es el único backend.**

Escrito aquí porque es la única forma de que el stack único no se convierta, con el tiempo,
en la grieta por donde se escapa el dominio.

### Por qué React y no Solid o Svelte

Es la alternativa más seria y merece respuesta honesta. Solid y Svelte 5 tienen reactividad
de grano fino, que **en teoría encaja mejor con datos que llegan decenas de veces por
segundo** — el caso de un terminal.

No los elijo por tres razones:

1. **El camino caliente no debe pasar por el framework, sea cual sea.** Ticks de precio,
   libro de órdenes y overlays del gráfico se escriben directo a canvas o DOM. Así se
   construyen los terminales reales, y con esa disciplina la ventaja de reactividad se
   aplica solo a la parte fría, donde no importa.
2. **Ecosistema de tablas densas.** TanStack Table y AG Grid son React primero, y Desk está
   lleno de tablas densas.
3. **Ya trabajas en React** (Expo), y el móvil futuro lo reutiliza.

(TanStack Start existe también para Solid, así que esta puerta queda abierta sin cambiar de
framework — pero la recomendación es React.)

### Forma del repositorio

Una sola aplicación, dos árboles de rutas:

```
apps/
├── web                TanStack Start
│   └── routes/
│       ├── (public)   Calls · prerenderizado
│       └── desk       Desk · ssr: false
└── mobile             Expo (después, DT-21)
packages/
├── tokens             variables CSS. Framework-agnóstico
└── ui                 componentes React compartidos de verdad
```

Con división de código por ruta, quien visita Calls **no descarga el terminal**. Y si algún
día conviene separar en dos despliegues, es configuración, no reescritura.

**Los tokens son variables CSS**, no un objeto de JavaScript: es lo que permite que el móvil
futuro consuma el mismo tema sin adaptadores, y lo que hace real el P1 de §3.

`packages/ui` gana mucho peso con esta decisión: con un solo stack, los componentes se
comparten **de verdad** entre lo público y el terminal, no solo la paleta.

### Gráficos: dos necesidades distintas

No fuerces una sola librería. **Calls** necesita gráficos de reporte —curva de equity,
barras de hit rate, calibración— que pide una librería ligera. **Desk** necesita gráfico de
velas con interacción y overlays de órdenes, que es la decisión D13 y es de otro orden.
Ambas deben seguir los tokens.

---

## 3. Sistema de diseño: la lección de Omarchy

Lo distintivo de Omarchy no son sus colores: es que **un tema se aplica de forma coherente a
todo el sistema** —escritorio, terminal, editor, notificaciones, pantalla de bloqueo— y que
las decisiones vienen curadas en vez de configurables hasta el infinito.

Traducido a esta plataforma:

### P1 — Un tema, todo el sistema

**Los tokens viven en un solo paquete** que consumen Calls, Desk y el móvil futuro: color,
tipografía, espaciado, radios, estados. Cambias el tema y cambia todo, incluido el modo
stream. Un token nunca se redefine dentro de una app.

### P2 — Temas con nombre, no un interruptor claro/oscuro

Como Omarchy: paletas completas y con identidad (Tokyo Night, Catppuccin, Gruvbox,
Everforest). Para ti tiene un uso concreto que un dark mode no tiene: **es tu identidad
visual en stream**, y puedes rotarla por temporada sin tocar una línea de layout.

### P3 — Tiling: paneles, nunca ventanas flotantes

Desk se organiza en paneles que se reparten la pantalla —gráfico, escalera, posiciones,
órdenes— sin solaparse. En un terminal de trading esto **no es estética**: es que no puedes
permitirte que un modal tape el precio mientras decides.

### P4 — Conducido por teclado

Paleta de comandos y atajos para todo lo frecuente. Es diferenciador real: los terminales
de los exchanges son pesados de ratón. Y ataca directamente una de las cinco categorías de
D29, la **carga cognitiva**: menos viajes al ratón, menos fricción entre decidir y ejecutar.

### P5 — Cifras monoespaciadas y tabulares

Los números se alinean por columna y no bailan al actualizarse. En datos financieros no es
gusto tipográfico: es poder comparar precios y tamaños de un vistazo, sin releer.

### P6 — Omakase: opinionado, no configurable

Pocos ajustes y buenos defaults. Es lo que hace que Omarchy se sienta cuidado en vez de
inacabado. Aplica especialmente a Desk: **las opciones de configuración son deuda**, y en un
terminal también son una forma de que el usuario se dispare en el pie.

---

## 4. Dónde Omarchy **no** aplica

**Calls es público.** Lo ve gente que no es desarrolladora, muchas veces desde el móvil, y
se muestra en stream a una audiencia amplia. Una estética de tiling window manager, densa y
para iniciados, trabaja en contra de la única función de esa página: **convencer a un
escéptico**.

La distinción operativa:

| | Calls | Desk |
|---|---|---|
| Tokens (color, tipografía) | **Los mismos** | **Los mismos** |
| Layout | Convencional, jerárquico, aireado | Tiling, denso |
| Densidad | Baja. Un dato importante por bloque | Alta. Todo a la vista |
| Móvil | **Requisito** | Secundario |
| Teclado | No hace falta | Central |

Comparten paleta y tipografía; no comparten densidad ni layout. Eso basta para que se
sientan el mismo producto sin que Calls herede la ergonomía de un terminal.

### El modo stream es un tercer layout

Doc 10 §8.3 lo pide: tipografía grande, alto contraste, sin navegación, legible a distancia.
No es Calls con zoom ni Desk simplificado — **es su propia vista, sobre los mismos tokens**.
Con P1, mantenerlo coherente sale gratis.

---

## 5. Consecuencias para decisiones abiertas

- **D13 (librería de charting)** gana un requisito: debe aceptar tematización desde tus
  tokens. Un gráfico que no puede seguir el tema rompe P1 justo en el elemento más visible
  de Desk. Hay que verificarlo **antes** de elegir, no después.
- **D29 (qué fallo de performance se ataca primero)** se conecta con P4: si la respuesta
  incluye carga cognitiva, buena parte se resuelve con teclado y preview en vivo, sin tocar
  el backend.
- **Accesibilidad de color**: rojo y verde son la señal principal en trading y también el
  tipo de daltonismo más común. Cada paleta debe validarse con contraste suficiente y no
  apoyarse **solo** en el color para distinguir compra de venta.
