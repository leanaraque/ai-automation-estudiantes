# Guía 5 (opcional) — El reporte mensual para Paula, en n8n

> **Opcional.** No se entrega ni suma a la nota: la Pre-entrega 6 es solo el PDF. Esta guía muestra cómo se ve en un flujo real lo que diseñamos en clase: el reporte de las consultas del mes, leído por Claude gastando lo mínimo.

## Por qué es un flujo nuevo y no el de la Semana 5

La pregunta de la clase decide: **¿alguien espera la respuesta?**

| | Flujo de la Semana 5 | Este flujo |
|---|---|---|
| Qué hace | Atiende cada consulta cuando llega | Lee todas las consultas del mes juntas |
| ¿Alguien espera? | Sí: Martina espera el WhatsApp | No: Paula lee el reporte el lunes |
| Cupón lote | No se puede: el lote tarda | Sí: la mitad de precio |
| Cupón caché | No aplica: el prompt es corto (menos de 1.024 tokens) | Sí: el manual tiene unos 2.260 tokens |

Los dos flujos conviven: el de la Semana 5 sigue avisando en el momento, y este arma el reporte una vez por mes.

## Qué configurás para pagar menos

Ningún cupón es automático. Ejecutar el flujo una vez por mes no alcanza, y los nodos de Anthropic que trae n8n no aplican ninguno. Los cupones se piden con un nodo **HTTP Request** (el de la Semana 4):

| Qué configurás | Qué cupón da |
|---|---|
| **El señalador:** `cache_control` al final del manual, en cada consulta, con el manual igual y primero | Cupón caché: el manual se lee 10 veces más barato |
| **La dirección del lote:** todas las consultas en un paquete, a `/v1/messages/batches` en lugar de `/v1/messages` | Cupón lote: todo a mitad de precio |
| **Esperar y retirar:** el flujo espera, pregunta si el lote terminó y descarga las respuestas | Ninguno: es lo que el lote pide a cambio |
| **Poner el manual sobre el escritorio** antes del lote: una llamada chica (`max_tokens: 0`) | Ayuda a que el cupón caché funcione dentro del lote (lo recomienda la documentación de Claude) |

## El flujo

```mermaid
flowchart TB
    subgraph P["1. EL MANUAL SOBRE EL ESCRITORIO"]
        direction LR
        T1["Probar ahora<br/>(Manual Trigger)"]:::ya --> M["El manual<br/>(parte fija de la Guía 1)"]:::ya
        T2["El día 1 de cada mes, 22:00<br/>(Schedule Trigger)"]:::ya --> M
        M --> W["Poner el manual sobre el escritorio<br/>HTTP Request · max_tokens 0<br/>+ señalador de 1 hora"]:::nuevo
    end
    subgraph C["2. LAS CONSULTAS DEL MES (como en la Semana 5)"]
        direction LR
        G["Las consultas del mes<br/>Gmail · Get many messages<br/>etiqueta Agencia Norte, últimos 30 días"]:::ya --> F{"No es respuesta<br/>automática<br/>(IF)"}:::ya
    end
    subgraph L["3. LOS DOS CUPONES"]
        direction LR
        PQ["Armar el paquete<br/>CUPÓN CACHÉ: cada consulta con<br/>el mismo manual y el mismo señalador"]:::nuevo --> E["Enviar el lote<br/>CUPÓN LOTE: HTTP Request a<br/>/v1/messages/batches"]:::nuevo
    end
    subgraph R["4. ESPERAR Y RETIRAR"]
        direction LR
        ES["Esperar 1 minuto<br/>(Wait)"]:::nuevo --> PR["Preguntar si terminó<br/>(HTTP Request)"]:::nuevo --> IF{"¿Terminó?<br/>(IF)"}:::nota
        IF -- "no" --> ES
        IF -- "sí" --> RT["Retirar las respuestas<br/>(HTTP Request)"]:::nuevo
    end
    subgraph RP["5. EL REPORTE PARA PAULA"]
        direction LR
        AM["Abrir la maleta<br/>(como el Parse JSON<br/>de la Semana 5)"]:::ya --> AR["Armar el reporte<br/>servicios, presupuesto,<br/>urgencia y lo que costó"]:::ya --> EN["Enviar el reporte a Paula<br/>(Gmail · Send)"]:::ya
    end
    P --> C
    C -- "solo las que pasan el filtro" --> L
    L --> R --> RP

    classDef ya fill:#171717,stroke:#171717,color:#F8F2E8
    classDef nuevo fill:#FF632B,stroke:#FF632B,color:#F8F2E8
    classDef nota fill:#FDAB2E,stroke:#FDAB2E,color:#171717
    style P fill:#FFFFFF,stroke:#E0D7C9,color:#171717
    style C fill:#FFFFFF,stroke:#E0D7C9,color:#171717
    style L fill:#FFFFFF,stroke:#E0D7C9,color:#171717
    style R fill:#FFFFFF,stroke:#E0D7C9,color:#171717
    style RP fill:#FFFFFF,stroke:#E0D7C9,color:#171717
```

En negro, piezas que ya conocés. En naranja, las nuevas. En el lienzo de n8n cada tramo tiene una nota que lo explica, y los nodos Code traen comentarios.

## Qué necesitás

- Una cuenta de **n8n Cloud** (la prueba gratuita dura 14 días).
- Tu Gmail con la etiqueta **Agencia Norte** y los [correos de prueba de la Semana 5](../semana-05/correos-de-prueba.md). El flujo busca los de los últimos 30 días; si son más viejos, reenviátelos y ponelos en la etiqueta.
- Saldo en la consola de Claude: [platform.claude.com](https://platform.claude.com) > **Billing**. La API se paga aparte de la suscripción a Claude. Cada prueba cuesta alrededor de un centavo de dólar.

## Paso 1 · Importar el flujo

1. En n8n: **Create** (arriba a la izquierda) > **Workflow**.
2. Tres puntos (arriba a la derecha) > **Import from URL** y pegá:

```
https://raw.githubusercontent.com/leanaraque/ai-automation-estudiantes/main/semana-06/n8n/el-reporte-mensual-para-paula.json
```

   Si preferís el archivo: abrí [`n8n/el-reporte-mensual-para-paula.json`](./n8n/el-reporte-mensual-para-paula.json) > **Download raw file** > en n8n, tres puntos > **Import from File**.

## Paso 2 · Conectar las credenciales

**Claude (4 nodos HTTP Request):**

1. Creá la clave en [platform.claude.com/settings/keys](https://platform.claude.com/settings/keys) > **Create key**. **Elegí un workspace** (por ejemplo, Default): una clave sin workspace no funciona en n8n. Copiala: empieza con `sk-ant-` y se muestra **una sola vez**.
2. Doble clic en **Poner el manual sobre el escritorio** > **Credential for Anthropic** > **Create new credential** > pegá la clave en **API Key** > **Save**. n8n la prueba al guardarla.
3. En **Enviar el lote**, **Preguntar si terminó** y **Retirar las respuestas**, elegí esa misma credencial.

**Si al guardar aparece "Couldn't connect with these settings - Bad Request":** la clave no tiene workspace. Creá otra eligiendo un workspace. O, con la misma clave, activá **Add Custom Header** en la credencial: **Header Name** `anthropic-workspace-id` y **Header Value** el ID de tu workspace (empieza con `wrkspc_`; está en [platform.claude.com/settings/workspaces](https://platform.claude.com/settings/workspaces)).

**Gmail (2 nodos):**

4. Doble clic en **Las consultas del mes** > en la credencial, **Create new credential** > **Sign in with Google** > elegí tu cuenta y aceptá los permisos.
5. En **Enviar el reporte a Paula**, elegí esa misma credencial y en **To** cambiá `tu.correo@ejemplo.com` por tu correo.

Si tu etiqueta no se llama "Agencia Norte", cambiá la búsqueda en **Las consultas del mes** > **Filters** > **Search**: `label:agencia-norte` (en Gmail, los espacios de la etiqueta se escriben con guion).

n8n guarda los cambios solo, mientras editás.

> La clave es como la tarjeta de crédito de la API: va solo en la credencial, nunca en un nodo ni en el chat.

## Paso 3 · Probarlo

1. Clic en **Execute workflow** (abajo, al centro). El flujo arranca por **Probar ahora**.
2. Esperá. **Es normal que tarde varios minutos, aunque sean pocas consultas:** el lote entra en una fila con los de todos los clientes (el límite es 24 horas; casi siempre, menos de 1). Mientras tanto, **Esperar 1 minuto** se repite. Para ver que avanza, abrí **Preguntar si terminó**: `processing_status: in_progress` y `errored: 0` significan que está todo bien.
3. Cuando termina, te llega un correo como este:

```
Hola Paula, este es el reporte de las consultas del último mes.

Consultas analizadas: 2

QUÉ SERVICIOS PIDEN
- Landing page: 1
- Gestión de redes sociales: 1

CON QUÉ PRESUPUESTO
- Mencionan un monto: 0 de 2 (en total, USD 0)

CON QUÉ URGENCIA
- Quieren avanzar ya (prioridad 5): 1
- Interés concreto, sin apuro (3 o 4): 0
- Solo averiguan (1 o 2): 1

LAS MÁS URGENTES
- Martina Sosa <martina.sosa@ejemplo.com>: Landing para lanzamiento del sábado, presupuesto aprobado

LO QUE COSTÓ ESTE REPORTE (Claude Sonnet 5, con caché y lote)
- Sin cupones: US$ 0,0125. Con los dos cupones: US$ 0,0112.
- De cada 100, pagamos 90. Con 600 consultas pagaríamos unos 18.

(Reporte automático)
```

La respuesta automática (el correo 3 de la Semana 5) no aparece: la frenó el filtro, igual que en la Semana 5. Los montos exactos van a variar un poco.

## Cómo leer la factura

Mirá las dos últimas líneas del correo: son el ticket de 100 de la clase.

- **Con pocas consultas**, el ahorro se ve chico. Poner el manual sobre el escritorio se paga completo, y con 2 o 3 consultas pesa mucho en el total.
- **Con las 600 del mes**, ese costo se reparte entre todas y el ticket baja a unos 17 o 18: los dos cupones juntos, como en la matriz de tu pre-entrega (de US$ 3,74 a US$ 0,65).

Si querés ver consulta por consulta si encontró el manual abierto, abrí el nodo **Abrir la maleta**: en `factura`, `cache_read_input_tokens` son los tokens leídos del escritorio. Dentro de un lote, el caché no está garantizado; alguna consulta puede no encontrarlo.

## Paso 4 · Dejarlo andando solo

1. Revisá la fecha en **El día 1 de cada mes, 22:00**. Si tu caso es semanal, cambiá el Schedule Trigger a semanas y la búsqueda de Gmail a `newer_than:7d`.
2. Arriba a la derecha, **Publish**. Desde ese momento corre solo.

Lo que hace cada mes, sin que toques nada: pone el manual sobre el escritorio, trae las consultas del mes, manda el paquete con los dos cupones, espera, retira las respuestas y le manda el reporte a Paula.
