# Arquitectura — frontend

> Define stack, dirección visual y sistema de diseño. Sin código.
> Backend en [03-arquitectura.md](./03-arquitectura.md).

---

## 1. Son dos frontends, no uno

Calls y Desk tienen requisitos **opuestos**, y forzarlos al mismo stack perjudica a los dos:

| | **Calls** (público) | **Desk** (terminal) |
|---|---|---|
| Audiencia | Cualquiera, mucha desde el móvil | Tú y miembros, en escritorio |
| Necesita | SEO, carga rápida, compartible, legible en stream | Densidad, canvas, atajos, websockets vivos |
| Renderizado | Estático o incremental en servidor | Cliente, aplicación de una página |
| Métrica que importa | Tiempo hasta el primer render | Latencia de interacción |

No comparten layout ni renderizado. **Comparten tokens de diseño** (§3), que es lo que hace
que se sientan el mismo producto.

---

## 2. Stack — resuelve D21

### El criterio que descarta media lista

**Ya tienes backend.** El dominio vive en .NET (doc 03), y eso elimina de golpe el
principal valor que aportan los meta-frameworks de JavaScript: su capa de servidor.

Next.js, TanStack Start o Nuxt brillan cuando el framework **es** tu backend: server
components, server actions, rutas de API. Con un modular monolith en .NET detrás, esa capa
no es una ventaja, es **un segundo sitio donde puede acabar viviendo lógica de negocio** —
justo lo que DT-17 y las reglas de independencia de módulos intentan evitar.

Así que la pregunta no es "¿qué meta-framework?", sino:

- **Calls** necesita HTML rápido y bien indexado → generador de sitios.
- **Desk** necesita una SPA que habla con tu API → router y capa de datos, sin servidor JS.

### Calls → **Astro** con islas React

Content-heavy con interactividad selectiva es exactamente su caso. Los números son
contundentes: en sitios comparables, Astro envía del orden de **9 KB de JS frente a ~460 KB**
de un equivalente en Next, y gana en Core Web Vitals por defecto porque compila a HTML en
vez de ejecutar un render de React por página.

Eso importa aquí más que en un sitio cualquiera: **Calls es la página que ve alguien que
todavía no confía en ti**, muchas veces desde el móvil y con mala conexión. Es la cima del
embudo.

Las **Server Islands** (Astro 5) cubren la parte que sí es dinámica —el progreso de las
proyecciones activas hacia su target— sin renunciar a que el resto sea estático.

### Desk → **React + Vite + TanStack Router + TanStack Query**

SPA pura, sin servidor JS, hablando con la API de .NET.

- **TanStack Router** da rutas y **search params tipados y validados por esquema**. Para un
  terminal eso no es un detalle: convierte el estado de la pantalla en URL compartible y
  restaurable — `?symbol=BTCUSDT&layout=ladder&tf=4h`. Encaja con el diseño conducido por
  teclado y con el tiling: cada disposición es un enlace.
- **TanStack Query** para el estado del servidor: caché, revalidación, reintentos.
- **Vite** para el desarrollo.

**TanStack Start alcanzó v1.0 estable en marzo de 2026** y sería la elección si necesitaras
SSR. No lo necesitas: Desk va detrás de login y el SEO es irrelevante. Usar Start aquí sería
pagar una capa de servidor que ya tienes en .NET.

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

Elegir Solid para evitar la disciplina del punto 1 cambia un problema conocido y resoluble
por riesgo de ecosistema. No sale a cuenta.

### Forma del repositorio

```
apps/
├── calls          Astro + islas React
├── desk           React + Vite + TanStack
└── mobile         Expo (después, DT-21)
packages/
├── tokens         variables CSS: color, tipografía, espaciado. Framework-agnóstico
└── ui             componentes React compartidos por calls y desk
```

**Los tokens son variables CSS**, no un objeto de JavaScript. Es lo que permite que Astro,
React y un día Expo consuman el mismo tema sin adaptadores, y lo que hace real el P1 de §3.

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
