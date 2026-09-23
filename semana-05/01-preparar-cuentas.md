# Guía 1 — Preparar las cuentas

Antes de construir el escenario necesitás tres cuentas gratuitas: **Slack**, **Groq** y **Twilio**. Hacelas con calma; ninguna pide tarjeta de crédito.

Al terminar esta guía vas a tener:

- Un espacio de Slack con un canal `#leads`.
- Una API key de Groq guardada en un lugar seguro.
- Tu celular conectado al sandbox de WhatsApp de Twilio, y tu Account SID y Auth Token a mano.

Tiempo estimado: 25 minutos.

---

## Parte A — Slack: el centro de comando del equipo

Slack es donde el equipo de Agencia Norte va a recibir el aviso de cada consulta nueva.

1. Entrá a [slack.com/get-started](https://slack.com/get-started) y elegí **Crear un espacio de trabajo** (Create a workspace).
2. Registrate con tu correo y confirmá el código que te llega.
3. Nombre del espacio: `Agencia Norte`. Los pasos de invitar gente se pueden saltear.
4. Creá un canal: en la barra lateral, **Añadir canal** > **Crear un canal**, nombre `leads`.

Listo. Todavía no hay que conectar nada: la conexión con Make se hace desde Make, en la Guía 3.

---

## Parte B — Groq: el cerebro que lee los correos

Groq es un servicio que ejecuta modelos de lenguaje (IA) muy rápido. Lo vamos a usar para que lea cada correo y devuelva un resumen, una prioridad y el teléfono del cliente.

1. Entrá a [console.groq.com](https://console.groq.com) y registrate (podés usar tu cuenta de Google).
2. En el menú, entrá a **API Keys** y hacé clic en **Create API Key**.
3. Ponele un nombre que la identifique, por ejemplo `make-curso`, y confirmá.
4. **Copiá la clave en ese momento** y guardala en un lugar seguro (un gestor de contraseñas o una nota privada). Groq la muestra una sola vez.

> IMPORTANTE: la API key es como una contraseña. No la pegues en el chat de Zoom, en un documento compartido ni la muestres en una captura. Si se te filtra, borrala desde la misma pantalla de API Keys y creá otra.

---

## Parte C — Twilio: el sandbox de WhatsApp

Para enviar mensajes de WhatsApp desde una automatización se necesita la **API de WhatsApp Business**, y a esa API se entra a través de un proveedor oficial (BSP). Usamos **Twilio** porque tiene un **sandbox**: un entorno de prueba gratuito, con un número compartido, donde no hace falta tener un número de empresa verificado.

### C.1 Crear la cuenta

1. Entrá a [twilio.com/try-twilio](https://www.twilio.com/try-twilio) y creá una cuenta gratuita.
2. Twilio te va a pedir verificar tu correo y tu número de celular. Usá el mismo celular en el que tenés WhatsApp.
3. En las preguntas de bienvenida podés elegir: producto **WhatsApp**, uso **alertas o notificaciones**, lenguaje **sin código / no-code**.

### C.2 Unir tu celular al sandbox

1. En la consola de Twilio, entrá a **Messaging** > **Try it out** > **Send a WhatsApp message**.
   (Si no lo encontrás, usá el buscador de la consola y escribí `WhatsApp sandbox`.)
2. Vas a ver el número del sandbox, **+1 415 523 8886**, y un código con la forma `join palabra-palabra`.
3. Desde tu WhatsApp, enviá ese mensaje exacto (`join palabra-palabra`) al número **+1 415 523 8886**. También podés escanear el código QR que aparece en pantalla.
4. Twilio te responde por WhatsApp confirmando que te uniste.

> PARA RECORDAR: el sandbox solo puede escribirle a números que se unieron. Y tu unión dura **3 días**: si pasan más de 3 días, volvé a enviar el mensaje `join`.

### C.3 Encontrar tus credenciales

Make te va a pedir dos datos para conectarse con Twilio:

1. En la página principal de la consola (**Account Dashboard**), buscá el recuadro **Account Info**.
2. Copiá el **Account SID** (empieza con `AC...`).
3. Copiá el **Auth Token** (hacé clic en *Show* para verlo).

Guardalos junto a tu API key de Groq. El Auth Token también es una contraseña: mismo cuidado.

---

## Checklist antes de pasar a la Guía 2

- [ ] Tengo un espacio de Slack `Agencia Norte` con el canal `#leads`.
- [ ] Tengo mi API key de Groq guardada.
- [ ] Envié el mensaje `join` al sandbox y Twilio me confirmó por WhatsApp.
- [ ] Tengo mi Account SID y mi Auth Token de Twilio.

## Si algo falla

| Problema | Qué revisar |
|---|---|
| Twilio no me deja verificar mi celular | Escribilo con código de país. Para Argentina: `+54 9` y el número sin el 15. |
| Envié el `join` y no me responde | Revisá que el código esté escrito exacto, sin espacios de más, y que lo mandaste al **+1 415 523 8886**. |
| No encuentro "Try it out" en Twilio | Twilio cambia su consola seguido. Usá el buscador de la consola con `WhatsApp sandbox`. |
