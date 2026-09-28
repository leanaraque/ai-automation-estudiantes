# Guía 4 — Tu Pre-entrega 6

La consigna completa está en la plataforma. Esta guía la ordena y te muestra las cuatro piezas resueltas para Agencia Norte. **Para ver cómo queda armado, mirá el PDF de ejemplo: [`ejemplo-pre-entrega-6.pdf`](./ejemplo-pre-entrega-6.pdf).** **Tu PDF es sobre tu propio caso** (un proceso tuyo o de un cliente ficticio con muchas solicitudes que no son urgentes: reseñas, contratos, consultas, facturas).

## Qué se entrega

Un **PDF** con cuatro piezas:

| Pieza | Qué es | Criterio de la rúbrica | Peso |
|---|---|---|---|
| 1 | La plantilla de prompt: rol, "pensá paso a paso" y variables `{{ }}` separadas del texto fijo | Ingeniería de prompts avanzada | 40% |
| 2 | El flujo por lotes (Batch) con sus tres marcas de tiempo y por qué tu caso tolera la espera | Modelado del flujo por lotes | 25% |
| 3 | El bloque de prefijo elegido para `cache_control` | Ahorro por Prompt Caching (junto con la fila 3 de la Pieza 4) | 15% |
| 4 | La matriz de costos: ahorro por Batch y ahorro por Caching, **en filas separadas** | Ahorro por Batches (20%) + Caching (parte del 15%) | 20% |

---

## Pieza 1 — La plantilla de prompt

Molde: la plantilla completa de la [Guía 1](./01-la-plantilla-de-prompt.md). Lo que no puede faltar:

1. **Rol específico** y **objetivo detallado** ("Actuá como analista comercial senior de Agencia Norte... Tu objetivo: ...").
2. **"Pensá paso a paso antes de dar la conclusión"**, con los pasos concretos.
3. **Parte fija arriba** (rol, datos del negocio, reglas, ejemplos, formato).
4. **Parte variable abajo**, con cada dato que cambia entre `{{ }}`.

En el PDF, marcá visualmente dónde termina la parte fija y empieza la variable (una línea o un título). Es lo que conecta con la Pieza 3.

## Pieza 2 — El flujo por lotes y sus tres marcas de tiempo

Ejemplo de Agencia Norte:

```
t0 ── 1. ENVÍO DEL LOTE · Viernes 22:00
      POST /v1/messages/batches
      Las 600 consultas del mes en UN solo envío (una solicitud por consulta).
      Respuesta inmediata: un ID de lote y el estado "in_progress".
      │
      │  2. VENTANA DE PROCESAMIENTO · hasta 24 horas
      │  (la mayoría de los lotes termina en menos de 1 hora)
      │  Todavía no hay resultados. Es la espera que "pagamos"
      │  a cambio del 50% de descuento.
      │
t1 ── 3. RECUPERACIÓN · Sábado
      Se consulta el lote hasta que el estado es "ended"
      y se descargan los 600 resultados juntos
      (quedan disponibles 29 días).
      │
      └─> Lunes 9:00: reporte mensual para la directora.
```

**Por qué el caso tolera la espera** (obligatoria en tu PDF, una línea): *"El reporte se revisa el lunes y las consultas se procesan el viernes a la noche: nadie espera la respuesta en pantalla. Si necesitáramos respuesta en segundos, como al atender a una clienta, Batch no aplicaría."*

Así se ve cada solicitud dentro del lote (una por consulta):

```json
{
  "custom_id": "consulta-001",
  "params": { "...": "el mismo pedido de la Pieza 3, con los datos de la consulta 001" }
}
```

El `custom_id` sirve para saber a qué consulta corresponde cada resultado: los resultados pueden volver en cualquier orden.

## Pieza 3 — El bloque de prefijo para `cache_control`

En criollo: el **prefijo** es la parte fija (el manual), el **caché** es la memoria rápida de Claude (el escritorio) y `cache_control` es la marca "guardar hasta acá" (el señalador). La marca va en el **último bloque de la parte fija**: todo lo que está antes se guarda; lo que viene después cambia en cada consulta.

```json
{
  "model": "claude-sonnet-5",
  "max_tokens": 1024,
  "system": [
    {
      "type": "text",
      "text": "Actuá como analista comercial senior de Agencia Norte... (toda la PARTE FIJA de la Pieza 1: rol, catálogo, información general, reglas, ejemplos y la instrucción de pensar paso a paso; unos 2.261 tokens)",
      "cache_control": { "type": "ephemeral", "ttl": "1h" }
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Remitente: {{remitente}}\nFecha: {{fecha}}\nConsulta:\n{{consulta}}"
    }
  ]
}
```

Qué explicar en el PDF, en dos o tres líneas:

- **Qué bloque** elegiste y **por qué es idéntico** en todas las llamadas.
- **Que supera el mínimo** cacheable de tu modelo (1.024 tokens en Sonnet 5, 4.096 en Haiku 4.5, 512 en Opus 5).
- **Qué TTL** usás: `"1h"` si va dentro de un lote (el lote puede tardar más de 5 minutos); el de 5 minutos (sin `ttl`) si las llamadas van seguidas en tiempo real.

Fijate que las variables están **fuera** del bloque cacheado, en `messages`. Ese es todo el truco.

## Pieza 4 — La matriz de costos

Usá la [calculadora](./03-calculadora-de-costos.md) con tus números. El ejemplo de Agencia Norte:

**Supuestos** (escribilos siempre: es lo que hace verificable la cuenta): 600 consultas · parte fija 2.261 tokens · parte variable 177 · salida 135 (medidos en el Playground de Claude) · Claude Sonnet 5 (US$ 2 entrada, US$ 10 salida, US$ 2,50 escritura de caché 5 min, US$ 4 escritura 1 h, US$ 0,20 lectura, por millón de tokens).

| Fila | La cuenta | Resultado |
|---|---|---:|
| Base, sin optimizar | Entrada: 1.462.800 tokens x US$ 2 por millón = US$ 2,93 · Salida: 81.000 x US$ 10 por millón = US$ 0,81 | US$ 3,74 |
| **Ahorro por Message Batches (-50%)** | US$ 3,74 x 50% | **- US$ 1,87** |
| **Ahorro por Prompt Caching** | Prefijo sin caché: 1.356.600 x US$ 2 = US$ 2,71 · Con caché: 1 escritura (2.261 x US$ 2,50) + 599 lecturas (1.354.339 x US$ 0,20) = US$ 0,28 · 90% menos sobre el prefijo | **- US$ 2,44** |
| Los dos combinados | Primero el caché (1 h), después el 50% sobre ese resultado | US$ 0,65 (82,6% menos) |

> Los dos ahorros **no se suman**: actúan sobre bases que se solapan (el prefijo está adentro de la entrada del lote). Un PDF que dice "50% + 90% = 140% de ahorro" está mal. Y ojo con el vocabulario: lo que baja es el **costo por token**, no la cantidad de tokens.

---

## Checklist antes de subir

- [ ] Pieza 1: rol + objetivo, "pensá paso a paso", variables en `{{ }}`, parte fija arriba y variable abajo, marcadas.
- [ ] Pieza 2: las tres marcas de tiempo (envío, ventana de hasta 24 horas, recuperación) y la línea de por qué tu caso tolera la espera.
- [ ] Pieza 3: el bloque con `cache_control` en el último bloque fijo, y por qué supera el mínimo de tu modelo.
- [ ] Pieza 4: supuestos escritos, fila de Batch y fila de Caching **separadas**, cada una con su cuenta.
- [ ] Precios confirmados en la página oficial el día que entregás.
- [ ] Un único PDF, con el texto seleccionable (no capturas de texto).
