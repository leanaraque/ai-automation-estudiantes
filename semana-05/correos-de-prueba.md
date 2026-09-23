# Correos de prueba

Usá estos correos para simular consultas de clientes de Agencia Norte. Envialos **desde otra cuenta de correo** (una secundaria, o la de alguien de confianza) a tu dirección de pruebas:

```
tucorreo+norte@gmail.com
```

(Reemplazá `tucorreo` por tu usuario de Gmail. Lo que va después del `+` es un alias: el correo llega a tu misma casilla, pero lo podemos separar con una etiqueta. Ver Guía 3, Paso 1.)

> IMPORTANTE: en el Correo 1, reemplazá el número por **tu propio celular**, el mismo que uniste al sandbox de Twilio. El sandbox solo puede escribirle a números que se unieron.

---

## Correo 1 — Urgente (debería salir por Slack y por WhatsApp)

**Asunto:** Landing para el lanzamiento de este sábado

```
Hola, ¿cómo están?

Soy Martina Sosa, de Panadería Sosa. Este sábado lanzamos nuestra línea de tortas por encargo y necesitamos una landing page con formulario de pedidos. Ya tengo el presupuesto aprobado y quiero arrancar hoy mismo.

¿Pueden llamarme hoy? Mi WhatsApp es +54 9 11 2233-4455.

Gracias,
Martina
```

Resultado esperado: prioridad **5**, teléfono **+5491122334455** (o el tuyo), aviso en Slack **y** WhatsApp.

---

## Correo 2 — Sin urgencia (debería salir solo por Slack)

**Asunto:** Consulta de precios

```
Hola, buenas tardes.

Estoy averiguando cuánto sale el manejo mensual de redes sociales para una ferretería. No es para ahora, estamos armando el presupuesto del año que viene.

Saludos,
Julián Pereyra
```

Resultado esperado: prioridad **1 o 2**, teléfono vacío, aviso **solo** en Slack.

---

## Correo 3 — Respuesta automática (no debería pasar el primer filtro)

**Asunto:** Respuesta automática: fuera de la oficina

```
Gracias por tu mensaje. Estoy fuera de la oficina hasta el lunes y no tengo acceso al correo.
```

Resultado esperado: el filtro anti-bucle lo frena **antes** de llegar a la IA. No se gasta ninguna consulta a Groq, ni Slack, ni WhatsApp.

---

## Por qué estos tres

Con tres correos probás los tres caminos posibles del escenario: el urgente (las dos salidas), el normal (una salida) y el que no tiene que pasar (ninguna). Es lo que en la Semana 3 llamamos **probar cada rama**.
