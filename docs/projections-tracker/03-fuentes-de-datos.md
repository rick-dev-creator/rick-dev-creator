# Fuentes de datos de mercado

> Investigación transversal. Sirve al módulo 1 (decisión D1) y al módulo 2.
> Datos verificados en septiembre 2026. **Los límites de los planes gratuitos cambian
> seguido — reverificar antes de comprometerse.**

---

## 1. El hallazgo que ordena todo

Los dos módulos tienen necesidades de datos **radicalmente distintas**, y tratarlas como una
sola fue el error a evitar:

| | Módulo 1 (proyecciones) | Módulo 2 (ledger) |
|---|---|---|
| **Necesita** | OHLC intradía hasta 5 minutos | Precio actual + cierre diario |
| **Por qué** | Regla de desempate §5.4 y detección de toques | Solo valuar posiciones |
| **Histórico** | Sí, desde la publicación de cada proyección | Mínimo |
| **Coste en gratis** | **Difícil en acciones y forex** | **Cubierto gratis en las tres clases** |

**Consecuencia práctica: el módulo 2 no añade coste de datos.** Vive entero dentro de
planes gratuitos. Todo el problema de datos es del módulo 1, y solo en acciones y forex
intradía.

---

## 2. Crypto — resuelto y gratis

### CoinGecko (elegido para valuación)

- Plan Demo: **10,000 llamadas/mes**, con **API key gratuita obligatoria**.
- Límite por minuto: la documentación indica 100/min en Demo; el plan público histórico
  reporta ~30/min variable según tráfico. Para este uso, irrelevante (ver abajo).
- Excelente cobertura de altcoins y metadatos. No entrega OHLC intradía fino.

**El batching vuelve irrelevante el tope mensual.** `/simple/price?ids=bitcoin,ethereum,...`
trae **todas** tus posiciones en **una sola llamada**. Entonces el límite no depende de
cuántos activos tengas, sino de cada cuánto refrescas:

| Frecuencia | Llamadas/mes | ¿Cabe en 10k? |
|---|---|---|
| Cada 5 min | ~8,640 | Al filo, no |
| Cada 15 min | ~2,880 | Sí, cómodo |
| Cada hora | ~720 | Sobra muchísimo |
| Diario + botón manual | ~30–100 | Trivial |

Para un ledger privado de auto-auditoría, **diario más refresco a demanda** es de sobra.

### Binance API (complemento para el módulo 1)

Gratuita, sin key para datos públicos, sin tope mensual, y entrega **OHLC intradía
excelente** incluyendo velas de 1m y 5m. Es la fuente natural para el motor de resolución
en crypto. Limitación: solo pares listados en Binance, sin metadatos de proyecto.

**Recomendación crypto:** CoinGecko para valuación y catálogo, Binance para las velas del
motor de resolución.

---

## 3. Acciones — el punto doloroso

| Proveedor | Plan gratuito | Nota |
|---|---|---|
| **Finnhub** | **60 llamadas/min**, cotizaciones en tiempo real | El más generoso en frecuencia. Free tier centrado en US. Sin batch: una llamada por símbolo |
| **Twelve Data** | 800 llamadas/día, 8 créditos/min | **Cubre acciones + forex + crypto en una sola API.** Datos con retraso |
| **Stooq** | CSV EOD, sin key | Solo cierres diarios. Suficiente para el módulo 2, inútil para el módulo 1 |
| **Alpha Vantage** | **25 llamadas/día** | Efectivamente inservible hoy. Descartado |

**Conclusión:** para el **módulo 2** (solo cierres diarios), cualquiera sirve y Stooq o
Twelve Data bastan gratis. Para el **módulo 1** (OHLC intradía con histórico), el plan
gratuito no alcanza y habrá que pagar. Es el único coste recurrente real del proyecto.

---

## 4. Forex — gratis y sin fricción

### Frankfurter (recomendado)

- **Sin API key, sin cuotas diarias ni mensuales.** Solo rate limiting anti-abuso.
- 206 divisas, histórico desde 1999, alimentado por BCE y fuentes oficiales.
- Resuelve también la conversión de divisa si el reporte va en MXN (decisión D7).

**Limitación crítica:** son **tipos de referencia diarios del BCE**, publicados una vez al
día. No son precios negociables ni tienen intradía.

- Módulo 2 → **perfecto**, es exactamente lo que necesita.
- Módulo 1 → **no sirve**. Una proyección de EURUSD con invalidación intradía no se puede
  resolver con un tipo de referencia diario. Requiere Twelve Data u otro proveedor de FX.

---

## 5. Recomendación

### Módulo 2 — coste cero, tres clases cubiertas

| Clase | Fuente | Coste |
|---|---|---|
| Crypto | CoinGecko Demo | Gratis (key requerida) |
| Acciones | Stooq o Twelve Data | Gratis |
| Forex | Frankfurter | Gratis, sin key |

### Módulo 1 — resuelve la decisión D1

- **Crypto:** Binance. Gratis y de calidad. Sin decisión pendiente.
- **Acciones y forex:** requieren plan de pago. **Twelve Data** es el candidato natural
  porque cubre ambas clases en una sola integración; el tier gratuito (800/día) alcanza
  para desarrollar y validar el motor antes de pagar nada.

Esto refuerza la recomendación del documento 01 §13.3: **construye y valida el motor de
resolución con crypto**, donde los datos son gratuitos, limpios y continuos. Habilita
acciones y forex cuando el motor esté probado, que es también cuando tendrás claro qué plan
de pago necesitas — en vez de adivinarlo hoy.

---

## 6. Principios de diseño derivados

1. **Abstracción por capacidad, no por proveedor.** Separar "precio actual" de "OHLC
   histórico": son necesidades distintas, con proveedores y costes distintos.
2. **Fuente sellada por símbolo** (módulo 1 §6.1). Cambiar de proveedor no debe reescribir
   resoluciones pasadas.
3. **Toda vela consultada se guarda.** El histórico propio reduce la dependencia del
   proveedor y permite reevaluar el récord completo con los mismos datos originales.
4. **Batch siempre que exista.** Es la diferencia entre caber en un plan gratuito y no caber.
5. **Solo se consultan símbolos vivos**: con proyección activa o posición abierta.

---

## Fuentes

- [CoinGecko API — Portfolio Tracking](https://docs.coingecko.com/docs/portfolio-tracking)
- [CoinGecko API — Pricing y rate limits](https://www.coingecko.com/en/api/pricing)
- [CoinGecko — Rate limit del plan público](https://support.coingecko.com/hc/en-us/articles/4538771776153-What-is-the-rate-limit-for-CoinGecko-API-public-plan)
- [Finnhub — Rate limits](https://finnhub.io/docs/api/rate-limit)
- [Frankfurter — API gratuita de tipos de cambio](https://frankfurter.dev/)
- [ExchangeRate.host](https://exchangerate.host/)
- [Comparativa de APIs financieras gratuitas 2026](https://dev.to/nexgendata/best-free-stock-market-apis-and-data-tools-in-2026-a-developers-honest-comparison-1926)
