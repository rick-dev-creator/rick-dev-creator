# Módulo 3 — Terminal de Trading Multi-Exchange

> Documento de evaluación estratégica, no especificación. El módulo 3 no es el siguiente
> paso de los módulos 1 y 2: es un producto distinto, con modelo de negocio, riesgo legal y
> escala de ingeniería propios.

---

## 1. Lo primero, con claridad

Los módulos 1 y 2 son proyectos de semanas. **El módulo 3 es un proyecto de años y, en
cuanto tiene usuarios, es una empresa.** No por dificultad conceptual, sino por tres cosas
que los módulos 1 y 2 no tienen:

1. **Custodia de autoridad de trading ajena.** Guardar API keys de terceros te convierte en
   el punto donde se pierde el dinero de otros si algo falla.
2. **Responsabilidad de ejecución en tiempo real.** Si un stop loss no dispara porque tu
   servidor estaba caído, el usuario perdió dinero por tu culpa. Y te lo va a cobrar.
3. **Obligación operativa continua.** Un SaaS de trading no puede estar caído. Los módulos
   1 y 2 pueden estar caídos un fin de semana sin que pase nada.

Esto no es un argumento para no construirlo. Es un argumento para **no dejar que retrase
los módulos 1 y 2**, que son los que crean la audiencia que hace viable el módulo 3.

---

## 2. El modelo de negocio es lo mejor de la idea

Y conviene decirlo porque cambia todo el alcance.

Tienes partnerships de afiliado con exchanges donde recibes **60% de los fees** de los
traders que traes. Eso significa que **el terminal no es el producto: es el vehículo de
adquisición y retención del ingreso de afiliado.** La suscripción es secundaria.

Las consecuencias de esto son enormes para el diseño:

- **No necesitas paridad de funciones con los terminales existentes.** Necesitas lo mínimo
  para que alguien se registre bajo tu link y siga operando ahí.
- **El acceso gratis mientras opere bajo tu referido no es una concesión, es el motor.**
  Cada usuario gratis es un usuario que te genera fees. El "gratis" es más rentable que la
  suscripción.
- **La métrica de éxito no son suscriptores, son referidos activos operando.**
- Un terminal mediocre con buen onboarding de afiliado gana dinero. Un terminal excelente
  sin él, no.

Este modelo está probado y es sólido. El error sería construir el terminal como si el
producto fuera el terminal.

### El conflicto de interés que hay que gestionar

Ganas un porcentaje del **volumen**, no del rendimiento de tus usuarios. Tu incentivo
económico y el interés de tu comunidad **apuntan en direcciones opuestas**: tú ganas más si
operan más, ellos suelen ganar más si operan menos.

Combinado con el módulo 1 (tú publicas las proyecciones que ellos siguen), esto es visible
y va a ser señalado. La respuesta correcta es **divulgación explícita y permanente**, no
esconderlo: decir en el sitio y en los videos que recibes parte de los fees. Es lo ético y
además te protege. Un creador que lo declara de frente es mucho más difícil de atacar que
uno al que se lo descubren.

---

## 3. La decisión arquitectónica que define el riesgo

Es una sola y condiciona todo lo demás:

### ¿Dónde viven las API keys?

**Opción A — Cliente (keys nunca salen del navegador del usuario)**

- El navegador firma y habla directo con el exchange. Tú nunca tocas las credenciales.
- Riesgo de custodia: **cero**. Si te hackean, no hay nada que robar.
- CCXT está diseñado así: *"as a client-side library, your API keys and funds never leave
  you"*.
- **Limitación dura:** nada funciona con el navegador cerrado. Sin trailing stops del lado
  servidor, sin estrategias que corran solas, sin alertas de ejecución.

**Opción B — Servidor (tú guardas las keys cifradas)**

- Habilita estrategias persistentes, trailing stops, automatización, ejecución 24/7.
- **Te vuelves el objetivo.** Un servidor con las keys de trading de N usuarios es un
  blanco de altísimo valor. Una sola brecha es un evento de extinción del proyecto y
  posiblemente personal.
- Exige, sin excepciones: cifrado por usuario con KMS/HSM, **nunca permisos de retiro**,
  IP whitelisting en el exchange, auditoría, y un plan de respuesta a incidentes.

**Recomendación: empieza en A y mueve a B solo la funcionalidad que lo exija de verdad**,
con las keys de esa funcionalidad separadas y con permisos mínimos. La mayoría de lo que
un trader discrecional quiere (ver posiciones, mover SL/TP visualmente, entrar y salir)
funciona perfectamente en A.

Y una regla que no se negocia: **usa órdenes nativas del exchange siempre que existan.** Un
stop loss que vive en el exchange sobrevive a tu servidor caído. Uno que vive en tu backend,
no. Cada stop "sintético" que implementes es una demanda potencial.

---

## 4. Dónde está la dificultad real de ingeniería

No está en conectarse a un exchange. Está en conectarse a varios y que el resultado sea
coherente.

- **Normalización.** Cada exchange difiere en nombres de símbolos, precisión, tipos de
  orden, reglas de tamaño mínimo y estructura de fees. Tipos como OCO, reduce-only,
  post-only o trailing stop existen de forma distinta, o no existen.
- **Reconciliación.** Tu estado interno se desincroniza del exchange: websockets que caen,
  fills perdidos, órdenes rechazadas. **Mostrar una posición incorrecta en un terminal de
  trading es catastrófico** — el usuario actúa sobre información falsa. Necesitas un bucle
  de reconciliación contra el estado real del exchange, no confiar en el stream.
- **Rate limits por exchange**, cada uno con su esquema de pesos.
- **Idempotencia de órdenes.** Un reintento mal hecho duplica una posición real con dinero
  real.

### Dos dependencias que te ahorran meses

**CCXT tiene paquete oficial de .NET** (`dotnet add package ccxt`): API unificada para 100+
exchanges, incluyendo streaming por websocket de tickers, libros, trades y órdenes. No
escribas esto a mano. Resuelve la mayor parte del problema de normalización.

**Charting.** Para arrastrar SL/TP visualmente sobre el gráfico necesitas una librería con
soporte de dibujo y overlays de órdenes. TradingView Lightweight Charts es libre pero no
trae herramientas de dibujo ni gestión de órdenes; la Charting Library completa cubre eso
pero requiere solicitud y aprobación de licencia. **Verifica y tramita esto temprano**: es
un bloqueo externo con tiempos que no controlas, y define buena parte de la UX del producto.

---

## 5. Aquí sí, Orleans

En el módulo 1 te habría dicho que Orleans es sobreingeniería: 5–20 proyecciones por semana
no justifican el modelo de actores.

**El módulo 3 es exactamente su caso de uso.** Un grain por cuenta-de-usuario-en-exchange,
uno por posición, uno por estrategia activa: estado en memoria, concurrencia aislada por
entidad, fan-in de websockets, ejecución con estado que sobrevive a reinicios. Es
literalmente el problema que Orleans resuelve, y es tu especialidad.

Es la primera vez en este proyecto donde tu stack por defecto y el problema coinciden.

---

## 6. Secuencia recomendada

La recomendación más importante del documento: **no construyas un terminal. Construye la
conectividad primero, en solo lectura.**

### 3a — Agregación de solo lectura (tus cuentas)

Ver posiciones, balances y órdenes abiertas de todos tus exchanges en una sola pantalla.
Sin colocar órdenes.

Esto **valida la parte difícil** (conectividad, normalización, reconciliación, websockets)
con **cero riesgo de ejecución y cero responsabilidad**. Si esto no funciona bien, nada de
lo demás importa. Y por sí solo ya te resuelve un dolor real.

Bonus: alimenta el módulo 2 automáticamente. Las posiciones que hoy capturarías a mano en
el ledger podrían entrar solas.

### 3b — Ejecución, solo para ti

Colocar y gestionar órdenes, SL/TP visual. Un único usuario: tú. Sin custodia de terceros,
sin SaaS, sin responsabilidad externa. Es donde descubres si la ejecución es fiable antes
de que lo haga un cliente.

### 3c — Multi-usuario y SaaS

Autenticación, gestión de keys de terceros, vinculación con el programa de afiliados,
suscripciones, soporte, SLA, on-call. **Este es el salto de proyecto a empresa**, y no
deberías darlo hasta que 3b lleve meses funcionando sin fallos con tu propio dinero.

---

## 7. Riesgos

| Riesgo | Gravedad | Mitigación |
|---|---|---|
| Brecha con keys de terceros | **Existencial** | Keys en cliente (§3); nunca permisos de retiro; IP whitelist |
| Stop loss que no dispara | **Alto** | Órdenes nativas del exchange, nunca sintéticas en tu backend |
| Posición mostrada incorrecta | **Alto** | Bucle de reconciliación; nunca confiar solo en el websocket |
| Licencia de charting bloqueada | Medio | Tramitar temprano; plan B con Lightweight Charts |
| Licenciamiento por ofrecer el terminal a terceros | Medio–alto | Asesoría legal antes de 3c, no después |
| Conflicto de interés por fees | Medio | Divulgación explícita y permanente (§2) |
| El módulo 3 devora los módulos 1 y 2 | **Alto y probable** | Secuencia de §6; no empezar 3a hasta que el módulo 1 esté en producción |

El último es el que más veces mata este tipo de proyecto. El módulo 3 es más emocionante de
construir que el módulo 1, y por eso es el que se lleva el tiempo.

---

## 8. Decisiones abiertas

| # | Decisión | Impacto |
|---|---|---|
| D11 | Keys en cliente o en servidor | **Máximo** — define el perfil de riesgo entero |
| D12 | Qué exchanges en 3a y en qué orden | Alto — empezar por aquel donde más operas |
| D13 | Librería de charting y su licencia | Alto — bloqueo externo, tramitar ya |
| D14 | ¿Spot, futuros, o ambos? | Alto — futuros multiplica la complejidad (margen, liquidación, funding) |
| D15 | Jurisdicción de la entidad que opera el SaaS | Alto — determina el marco regulatorio; requiere abogado |
| D16 | ¿3a alimenta automáticamente el ledger del módulo 2? | Medio — sinergia evidente, pero acopla los módulos |

---

## Fuentes

- [CCXT — API unificada para 100+ exchanges, con soporte C#/.NET](https://github.com/ccxt/ccxt)
- [CCXT — Documentación](https://docs.ccxt.com/)
