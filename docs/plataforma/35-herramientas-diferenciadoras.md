# Herramientas diferenciadoras del terminal

> Resuelve parcialmente **D23**. Es la tesis de valor del módulo 3: la razón por la que
> alguien usaría tu terminal en vez del que ya trae su exchange.
> Documento abierto — falta enumerar el resto de herramientas.

---

## 1. Las dos razones del terminal

Declaradas, y conviene separarlas porque solo una justifica el proyecto:

1. **Unificar tus operaciones en un solo lugar sin pagar suscripciones ajenas.**
2. **Tener herramientas personalizadas que los exchanges no ofrecen al nivel que quieres.**

La razón 1, sola, **no justifica construirlo**: una suscripción a un terminal existente
cuesta mucho menos que los meses de ingeniería que esto lleva. Es un buen motivo secundario,
no una tesis.

La razón 2 sí. Y como la razón 2 es lo que manda, hay una consecuencia directa de alcance:
**el MVP del módulo 3 no es un terminal completo, es la primera herramienta diferenciadora
sobre la agregación de solo lectura.** No compitas con productos maduros en las funciones
que ya hacen bien; compite donde no llegan.

---

## 2. H1 — Escalera de órdenes piramidal, visual

La primera herramienta declarada, y sirve de arquetipo para las demás.

### Qué es

Colocar N órdenes límite distribuidas en un rango de precio, con una curva de tamaños
configurable, para longs y shorts, manipulada **visualmente sobre el gráfico**.

### Dónde se quedan cortos los exchanges

Varios ofrecen *scaled orders* básicas, pero:

- Se configuran en un formulario, no arrastrando sobre el gráfico.
- No te muestran **en vivo, mientras ajustas**, cuál sería tu entrada promedio resultante.
- No relacionan la escalera con tu nivel de invalidación ni con el R resultante.
- No tratan la escalera como **una sola cosa**: una vez enviada, son N órdenes sueltas que
  tienes que mover o cancelar una por una.

### Lo que la hace valiosa

El preview en vivo mientras arrastras: **entrada promedio, tamaño total, R resultante
contra tu invalidación y, en apalancado, precio de liquidación.** Eso es lo que ningún
exchange te enseña mientras armas la escalera, y es exactamente lo que necesitas para
decidir si la escalera está bien puesta.

### La buena noticia arquitectónica

Las órdenes de la escalera son **órdenes límite nativas del exchange**, en reposo en su
libro. No son sintéticas ni dependen de que tu backend esté vivo.

Consecuencia: **tu herramienta insignia funciona con las keys en el cliente** (D11) y
sobrevive a que tu servidor se caiga. La funcionalidad más diferenciadora del producto no
te obliga a la arquitectura arriesgada. Esto refuerza mucho la recomendación de empezar con
keys en cliente.

### Modelado: la escalera es un objeto de primera clase

El exchange solo conoce órdenes individuales. Tú necesitas moverla, cancelarla y evaluarla
**como una unidad**. Así que la escalera existe en tu sistema como entidad propia:

```
OrderLadder
├── Direction            Long | Short
├── PriceRange           desde / hasta
├── LevelCount           N órdenes
├── Distribution         Uniforme | Lineal | Geométrica | Personalizada
├── TotalSize            tamaño agregado
├── ChildOrders[]        → IDs reales en el exchange
├── LinkedProjection     → Projection del módulo 1 (§2.1)
└── State                Borrador | Activa | ParcialmenteLlena | Completada | Cancelada
```

La **distribución** es lo que la vuelve "piramidal": más tamaño conforme el precio mejora
(o lo contrario, según la estrategia). Debería ser configurable y guardable como plantilla
reutilizable.

### 2.1 La integración que nadie más puede copiar

Tu terminal tiene algo que ningún competidor tiene: **tus proyecciones del módulo 1 viven
en el mismo sistema.**

Eso permite pre-cargar una escalera directamente desde una proyección publicada: la zona de
entrada define el rango, la **invalidación define el stop loss automáticamente**, y los
targets definen la escalera de salida. Un clic desde tu propio análisis a la orden puesta.

Es integración vertical pura y es inimitable: un terminal genérico no sabe cuál es tu tesis.

### 2.2 Lo genuinamente difícil

No es dibujar la escalera. Es esto:

- **Notional mínimo por orden.** Partir el tamaño en 10 puede dejar cada tramo por debajo
  del mínimo del exchange. El sistema tiene que avisarlo *antes* de enviar, no fallar a la
  mitad.
- **Redondeo de tick y lot size.** Distribuir tamaño entre niveles acumula deriva; el
  agregado real difiere del pedido. Hay que decidir dónde se absorbe la diferencia.
- **Llenados parciales.** La entrada promedio cambia en cada fill. El R mostrado tiene que
  recalcularse en vivo o miente.
- **No hay atomicidad.** Mover o cancelar una escalera son N llamadas que pueden fallar a
  la mitad, dejándote con media escalera puesta. Necesitas estado de reconciliación
  explícito, no asumir éxito.
- **Límite de órdenes abiertas** por símbolo y por cuenta, distinto en cada exchange.
- **Reduce-only y modo de posición** en shorts y en futuros, con semántica distinta por
  exchange.

Ninguno es insalvable. Todos son la razón por la que esto tarda más de lo que parece.

---

## 3. Las demás herramientas — pendiente

Mencionaste que quieres "muchas otras" que los exchanges no ofrecen al nivel que buscas.
**Esta sección está vacía a propósito y es el siguiente paso del módulo 3.**

Para cada una, basta responder cuatro cosas:

1. **Qué hace**, en una frase.
2. **Qué hace hoy el exchange** y dónde exactamente se queda corto.
3. **¿Usa órdenes nativas o requiere lógica viva en servidor?** — determina si cae del lado
   seguro de D11 o del arriesgado.
4. **¿Se conecta con el módulo 1?** — si sí, es inimitable.

La pregunta 3 es la que más importa. Divide tus herramientas en dos grupos:

| | Órdenes nativas | Lógica viva en servidor |
|---|---|---|
| Ejemplos | Escaleras, OCO, SL/TP nativos | Trailing stops propios, entradas condicionales, automatización |
| Riesgo | **Bajo** — keys en cliente, sobrevive a caídas | **Alto** — custodia de keys, responsabilidad si falla |
| Cuándo | Desde el principio | Solo si el valor lo justifica, y con keys separadas |

Si resulta que casi todas tus herramientas son del primer grupo, el módulo 3 es mucho menos
arriesgado y mucho más barato de lo que parecía. Vale la pena averiguarlo antes de diseñar
la arquitectura.

---

## 4. Qué se necesita para cerrar D23

Una lista con esas cuatro respuestas por herramienta. Con eso se puede:

- Elegir cuál es el MVP (la que más te duele hoy y menos servidor requiere).
- Decidir D11 con datos en vez de por precaución.
- Estimar de verdad el módulo 3.
