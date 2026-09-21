# Módulo 5 — Membresías y accesos

> **Módulo nuevo**, no contemplado en la planeación original. Salió al describir cómo
> funciona hoy la comunidad y cuál es el objetivo de negocio.

---

## 1. Por qué esto es un módulo

Hoy existe y funciona **a mano**:

```
Patreon cobra  →  se asigna rol en Discord  →  desbloquea canales privados
                                            →  invitación manual a indicadores de TradingView
```

Y el objetivo declarado es cambiar la fuente del derecho:

> "El objetivo es mover de Patreon a trading por volumen; gano mucho más ahí."

**Ese cambio no se puede hacer a mano.** Verificar que alguien opera bajo tu referido exige
consultar el programa de afiliados de cada exchange, vincular su cuenta con su usuario de
Discord y revisar periódicamente que siga calificando. Eso es software, y no lo cubre
ninguno de los módulos 1 a 4.

Es además, de todo lo planeado, **lo que más directamente mueve dinero**: no crea audiencia
nueva, convierte la que ya tienes a un canal de ingreso mejor.

---

## 2. Qué resuelve

| Hoy | Con el módulo 5 |
|---|---|
| Solo Patreon da acceso | Patreon, suscripción propia **o volumen bajo tu referido** |
| Asignación manual de roles | Automática al verificarse el derecho |
| Invitaciones a TradingView a mano | Cola de trabajo calculada (§5) |
| **Nadie revisa quién dejó de pagar** | Reconciliación periódica y revocación |
| Patreon se lleva su comisión | El camino de afiliado no tiene intermediario |

La cuarta fila es la fuga de dinero que ya tienes hoy y que nadie está mirando: gente con
acceso vigente que dejó de pagar hace meses.

---

## 3. Modelo de dominio

```
Member
├── DiscordUserId          identidad principal (la comunidad vive ahí)
├── ExchangeAccounts[]     UID por exchange, verificado (§4)
├── TradingViewUsername    para las invitaciones
├── Entitlements[]         derechos vigentes e históricos
└── Tier                   derivado, nunca asignado a mano

Entitlement
├── Source                 Patreon | Suscripción | VolumenAfiliado | Manual
├── Evidence               referencia al pago o al reporte de volumen
├── ValidFrom / ValidUntil
└── Status                 Activa | EnGracia | Expirada | Revocada

Grant                      efecto concreto de un derecho
├── Type                   RolDiscord | InvitaciónTradingView | AccesoTerminal
├── State                  Pendiente | Aplicado | PendienteDeRetiro | Retirado
└── AppliedAt
```

### Tres reglas que evitan que esto se pudra

1. **Todo derecho tiene fuente, evidencia y caducidad.** Un derecho sin fecha de fin nunca
   se revoca, y el sistema se llena de accesos zombi. Es el estado actual.
2. **El tier se deriva, nunca se asigna.** Si alguien puede tener un rol sin derecho que lo
   respalde, la reconciliación no sirve para nada. La excepción `Manual` existe (regalos,
   colaboradores) pero es un derecho explícito con caducidad, no un rol suelto.
3. **Gracia antes de revocar.** Un reporte de volumen tardío o una caída de la API de un
   exchange no debe expulsar a nadie. El estado `EnGracia` absorbe fallos transitorios
   antes de retirar el acceso.

---

## 4. El problema difícil: verificar volumen de afiliado

### Lo que sí existe

- **OKX** tiene endpoints de afiliado explícitos: consultar el detalle de un invitado,
  **comprobar si un usuario es tu invitado**, y generar datos de rebates descargables.
- **Bybit** tiene programa de API Brokers, con Broker ID asociado a tu UID y rebates según
  volumen y usuarios captados.
- **Binance** expone el `uid` de la cuenta y tiene programas de afiliado y broker.

El primitivo que necesitas —*dado un UID, ¿es mi referido y cuánto opera?*— **existe**, al
menos en OKX de forma documentada.

### Lo que hay que asumir

- **Es una integración por exchange.** CCXT no ayuda aquí: cubre trading, no programas de
  afiliados. Cada uno tiene su forma, su granularidad y su latencia.
- **El reporte no es en tiempo real.** Suele ser diario. La verificación de derechos es un
  proceso periódico, no un evento instantáneo.
- **La vinculación UID ↔ Discord es un flujo de onboarding real**, no un detalle: el usuario
  se registra bajo tu link, declara su UID, y tú confirmas contra el reporte de afiliado.

### La trampa que hay que planear ahora

**Quien ya tiene cuenta en un exchange normalmente no puede vincularla a un referido
después.** Es decir: **una parte de tu comunidad actual nunca podrá calificar por volumen**,
por mucho que quiera apoyarte.

Consecuencia directa: **la vía de pago por suscripción no se puede eliminar.** El objetivo
de "mover de Patreon a volumen" aplica a miembros nuevos y a quienes abran cuenta nueva, no
a toda la base. Planear una migración total llevaría a expulsar gente que sí quiere pagar.

---

## 5. TradingView: automatizar aquí sería un error

La gestión de acceso a scripts *invite-only* **no tiene API oficial**. Existe una vía
automatizada conocida, pero:

- Usa endpoints de backend **no soportados oficialmente** por TradingView.
- **Exige desactivar el 2FA** de tu cuenta de vendedor para que un programa pueda entrar.
- TradingView tolera el uso individual, pero puede cerrarlo cuando quiera.

Desactivar el 2FA de la cuenta que contiene tu propiedad intelectual, y guardar sus
credenciales en tu servidor, es un riesgo desproporcionado para ahorrar unos clics.

**Recomendación: semi-automático.** El sistema calcula quién *debería* tener acceso y
produce una **cola de trabajo con el diff** —"añadir a estos 3, quitar a estos 2"— y tú
haces los clics en TradingView.

Automatizas la parte difícil (saber quién califica, que hoy nadie sabe) y no automatizas la
parte peligrosa. El coste es un par de minutos por semana. El beneficio es no entregar tu
cuenta de vendedor a un script no soportado.

---

## 6. El conflicto de interés se vuelve más agudo aquí

PG3 aplica a todo el programa, pero este módulo lo tensa más que ningún otro: **si el acceso
depende del volumen operado, estás dando a tu comunidad un incentivo directo para operar de
más.** Eso es malo para ellos y, a la larga, malo para tu credibilidad —que es el activo
del que cuelga todo lo demás (PG1).

Mitigación concreta, y conviene decidirla ahora y no cuando ya esté construido:

- **Calificar por tener cuenta activa bajo tu referido, no por alcanzar un volumen alto.**
  Un umbral mínimo y simbólico cumple el mismo fin comercial sin empujar a nadie a operar
  más de la cuenta.
- **Nunca escalar los beneficios con el volumen.** En cuanto operar más da más acceso, el
  sistema está premiando exactamente la conducta que arruina a los traders minoristas.
- **Declararlo abiertamente**, igual que el reparto de fees.

Un umbral bajo te da casi el mismo ingreso, porque el ingreso de afiliado viene del agregado
de muchos usuarios, no de exprimir a unos pocos.

---

## 7. Notificaciones de inversión

El otro uso declarado de Discord: **avisar cuando inviertes en algo**, como beneficio del
tier alto.

Esto define una línea que conviene fijar explícitamente en todo el programa:

| | Público y gratis | Reservado a miembros |
|---|---|---|
| **Qué** | Las proyecciones y su récord resuelto | La notificación en tiempo real de lo que haces |
| **Por qué** | Es la prueba. Es el motor de confianza y la cima del embudo | Es la oportunidad. Tiene valor por ser oportuna |

**Lo que demuestra credibilidad es público; lo que tiene valor de oportunidad es de pago.**
Regalar el récord es lo que hace creíble el producto de pago; cobrar por el récord mataría
el embudo entero.

Nota técnica: estas notificaciones se alimentan de la agregación de solo lectura del módulo
3a. Hasta que exista, se capturan a mano — lo cual es perfectamente aceptable para empezar,
porque inviertes con poca frecuencia.

---

## 8. Dónde encaja en el roadmap

Este módulo **no depende del terminal** y puede construirse en paralelo o antes.

Sus dos mitades tienen costes muy distintos:

- **Mitad barata y de alto retorno:** registro de miembros, derechos con caducidad,
  reconciliación y revocación, roles de Discord automáticos, cola de TradingView. Resuelve
  la fuga de accesos zombi y quita trabajo manual. **No requiere ninguna integración con
  exchanges.**
- **Mitad cara:** verificación de volumen de afiliado, una integración por exchange, con
  onboarding de vinculación de UID.

**Recomendación: la mitad barata va justo después del módulo 1** —o incluso en paralelo, no
comparten casi nada— y la cara cuando quieras activar de verdad la migración desde Patreon.
