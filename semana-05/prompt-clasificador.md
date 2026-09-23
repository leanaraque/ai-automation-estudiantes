# Prompt del clasificador

Estas son las instrucciones que le damos a la IA (Groq) en el módulo **Create a JSON Chat Completion**. Van en dos mensajes, como en la Semana 3: uno con el rol **System** (quién es y qué reglas sigue) y otro con el rol **User** (el correo a analizar).

## Mensaje 1 — Role: System

Copialo tal cual:

```
Sos el asistente de triaje comercial de Agencia Norte, una agencia de diseño web y marketing digital.

Vas a leer el correo de un posible cliente y vas a responder SOLO con un objeto JSON que tenga exactamente estas tres claves:

- "resumen": qué necesita el cliente, en 10 palabras como máximo.
- "prioridad": un número entero del 1 al 5.
    5 = quiere avanzar ya o tiene una fecha límite en los próximos 3 días.
    3 = tiene interés concreto, pero sin urgencia.
    1 = solo está averiguando, sin fecha ni presupuesto.
- "telefono": el número de WhatsApp del cliente en formato internacional, empezando con + y sin espacios ni guiones. Ejemplo: +5491122334455. Si el correo no trae un teléfono, devolvé "".

Reglas:
- No agregues texto fuera del JSON.
- No inventes datos que no estén en el correo.
```

## Mensaje 2 — Role: User

Acá se combina texto fijo con **variables** del módulo de Gmail, como hiciste con `{{1.nombre}}` en la Semana 3. En Make, las partes entre corchetes se reemplazan arrastrando la "pastilla" correspondiente del módulo 1 (Gmail):

```
Remitente: [Sender name] <[Sender email address]>
Asunto: [Subject]
Correo:
[Text content]
```

> El nombre exacto de cada campo puede variar un poco según la versión del módulo de Gmail. Buscá los que contengan el nombre del remitente, su correo, el asunto y el texto del mensaje.

## Qué devuelve la IA

Para el correo urgente de prueba, la respuesta se ve así:

```json
{
  "resumen": "Landing para el sábado, presupuesto aprobado",
  "prioridad": 5,
  "telefono": "+5491122334455"
}
```

Es la misma "maleta de datos" (JSON) que viste en las Semanas 2 y 4: claves y valores. La diferencia con la Semana 3 es que ahora la IA no responde una sola palabra, sino **tres datos**, y cada uno va a un lugar distinto:

| Clave | A dónde va |
|---|---|
| `resumen` | Al mensaje de Slack, para que el equipo entienda el pedido sin abrir el correo. |
| `prioridad` | Al filtro del Router: decide si también se envía el WhatsApp. |
| `telefono` | Al destinatario del WhatsApp. |

Fijate que `prioridad` es un **número** (va sin comillas) y `telefono` es **texto** (va entre comillas). Eso importa al configurar el filtro.
