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

### Calls → **Astro con islas React**

Estático o incremental por defecto, JavaScript solo donde hace falta (los filtros del
scorecard, los gráficos). Es lo que da el mejor tiempo hasta el primer render, el mejor SEO
y las páginas más ligeras — que importa porque **es la cima del embudo**: la página que ve
alguien que aún no confía en ti, muchas veces desde el móvil y con mala conexión.

### Desk → **React con Vite, aplicación de una página**

Densa, con estado en cliente, canvas, atajos de teclado y websockets. Aquí el SEO es
irrelevante y la latencia de interacción lo es todo.

### Expo — no para esto, sí para después

Lo usas hoy, así que conviene ser explícito sobre por qué no encaja en ninguno de los dos:

- **Para Calls**: React Native Web no da SSR real ni SEO, y arrastra un bundle que penaliza
  justo la métrica que más importa en la página pública.
- **Para Desk**: pelea contra el ecosistema. Las librerías de charting de trading —incluida
  la de TradingView (D13)— son web primero y esperan DOM y canvas. Arrastrar precios con
  precisión sobre un gráfico es exactamente donde React Native Web se rompe.

**Pero Expo es la elección correcta para el móvil**, que está fuera de alcance hoy (doc 10
§10) y que un día querrás: consultar el récord, recibir avisos del Club, ver posiciones. Ese
día, Expo consume la misma API y los mismos tokens. **No se descarta: se pospone.**

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
