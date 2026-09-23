# Guía 4 — Tu Pre-entrega 5

La consigna completa está en la plataforma. Esta guía te ayuda a ordenarla y a conectarla con lo que hicimos en clase.

## Qué se entrega

**Un solo enlace público** (Google Doc o página de Notion) con dos partes:

| Parte | Qué es | Peso |
|---|---|---|
| 1 | Tabla de estrategia: los 5 escenarios de la clínica, con canal elegido, justificación y por qué descartaste los otros dos | 20% |
| 2 | Blueprint de Make: el JSON exportado, **pegado como texto**, más una captura del Run once exitoso | 80% |

Si entregás solo el blueprint, perdés el 20% completo. Hacé las dos partes.

---

## Parte 1 — La tabla de estrategia (clínica)

En clase practicamos la **matriz de canales** con los mensajes de Agencia Norte. Ahora aplicás el mismo razonamiento a los 5 escenarios de la clínica (A a E, en la consigna).

### El método: tres preguntas por escenario

1. **¿Quién tiene que enterarse?** ¿El paciente (externo) o el equipo (interno)?
2. **¿Qué tan urgente es?** ¿Hay consecuencias si no lo lee en minutos?
3. **¿Qué tiene que llevar el mensaje?** ¿Un documento largo, un aviso corto, una confirmación?

Y la matriz que vimos:

| Canal | Urgencia | Uso ideal | Lo que siente quien lo recibe |
|---|---|---|---|
| Email | Baja / media | Documentación, propuestas largas, adjuntos | "Es profesional y queda registrado" |
| Slack | Interna | Coordinación del equipo, alertas | "Estamos todos alineados" |
| WhatsApp | Alta | Recordatorios, confirmaciones urgentes | "Me dan prioridad personal" |

### Ejemplo resuelto (con Agencia Norte, no con la clínica)

> **Escenario:** una clienta escribe que necesita una landing para el sábado y pide que la llamen hoy.
>
> **Canal elegido: WhatsApp.** Es una venta con fecha límite en menos de 3 días: si esperamos a que lea un correo, puede contratar a otra agencia. WhatsApp tiene una **tasa de apertura** mucho más alta que el email y la clienta ya nos dio su número.
>
> **Por qué no Email:** es **asíncrono**; la respuesta puede tardar horas.
> **Por qué no Slack:** es un canal **interno**; la clienta no está ahí. (Sí avisamos al equipo por Slack, pero eso es otra acción.)

### Checklist de la Parte 1

- [ ] Una fila por escenario (A, B, C, D, E).
- [ ] En cada fila: canal elegido + justificación + por qué descartaste los otros dos.
- [ ] Usaste al menos tres conceptos técnicos del módulo. Por ejemplo: asíncrono, tasa de apertura, canal compartido, trazabilidad, ventana de 24 horas, plantilla aprobada.

> Pista para el Escenario A: fijate qué tiene que llevar el mensaje. Un PDF de 5 páginas no es lo mismo que un aviso de dos líneas.

---

## Parte 2 — El blueprint de Make

El escenario que construimos en clase ([Guía 3](./03-construir-escenario.md)) cubre los puntos que pide la consigna:

| La consigna pide | En nuestro escenario |
|---|---|
| Un trigger en Gmail conectado a un análisis de IA que clasifique la prioridad | `Consulta entrante` (Gmail) + `IA: resumen y prioridad` (Groq) |
| Un Router que derive al canal de acción inmediata cuando la prioridad es máxima | `¿Qué canales?` + filtro `Urgente con teléfono` (prioridad = 5) |
| Salida a Slack con resumen al equipo | `Avisar al equipo` (canal `#leads`) |
| Salida a WhatsApp API con mensaje dinámico en formato internacional | `WhatsApp urgente` (Twilio, `whatsapp:+549...`) |

Si querés, podés adaptar el prompt al contexto de la clínica. La estructura del escenario es la misma.

### Paso 1 — La captura del Run once

1. Enviá los tres correos de prueba y hacé **Run once** (Guía 3, Paso 8).
2. Entrá a la pestaña **History** del escenario y abrí la ejecución.
3. Sacá una captura donde se vea el recorrido: qué módulos se ejecutaron (con sus burbujas) y cuáles no.

> Antes de capturar: que no se vea ninguna clave, token ni número de teléfono real completo. Si aparece, tapalo en la imagen.

### Paso 2 — Exportar el blueprint

1. Con el escenario abierto, hacé clic en el menú de los tres puntos (**...**) de la barra inferior.
2. Elegí **Export Blueprint**. Se descarga un archivo `.json`.
3. Abrilo con el Bloc de notas (o cualquier editor de texto), seleccioná todo y copialo.

El blueprint **no incluye** tus claves: las conexiones quedan guardadas en tu cuenta de Make, no en el archivo. Igual, revisá que no hayas pegado ninguna clave dentro de un campo de texto.

### Paso 3 — Armar el documento

1. Creá un Google Doc: `Pre-entrega 5 - Tu nombre`.
2. Título **Parte 1**: tu tabla de los 5 escenarios.
3. Título **Parte 2**: pegá el JSON **como texto** (no como imagen) y, debajo, la captura del Run once.
4. **Compartir** > Acceso general: **Cualquier persona con el enlace** > **Lector**.
5. Abrí el enlace en una **ventana de incógnito**. Si te pide permiso, quien corrige tampoco va a poder abrirlo.

---

## Checklist final

- [ ] Un solo enlace público (Google Doc o Notion), probado en incógnito.
- [ ] Parte 1: tabla con los 5 escenarios, justificación, descarte de los otros dos canales y al menos 3 conceptos técnicos.
- [ ] Parte 2: JSON del blueprint pegado como texto.
- [ ] Parte 2: captura del Run once mostrando Gmail, IA, Router, Slack y WhatsApp con sus filtros.
- [ ] Ninguna clave visible en el documento ni en las capturas.
