# PRE-ENTREGA 6: DISEÑO DE EFICIENCIA E INGENIERÍA DE PROMPTS RECURRENTES

**Curso:** IA Automation  
**Estudiante:** [Nombre del Estudiante]  
**Fecha:** Octubre 2026  
**Caso de Negocio:** Clasificación y Triaje Automatizado de 2.500 Tickets de Soporte Técnico en FinTech  

---

## Resumen Ejecutivo del Proyecto

El presente informe documenta la estrategia de optimización técnica y financiera para procesar un volumen masivo recurrente de 2.500 incidentes mensuales reportados por usuarios de una plataforma de pagos digitales. Se aplican técnicas de **Prompt Caching** para reutilizar la base de conocimiento estática y **Message Batches API** para reducir al 50% el costo del procesamiento asíncrono no interactivo.

---

## PIEZA 1: Plantilla de Prompt Reutilizable (Ingeniería de Prompts)

> **Criterio de arquitectura:** El bloque estático e inmutable se define al inicio para garantizar su cacheabilidad. Las variables dinámicas delimitadas con `{{ }}` se ubican estrictamente al final.

### Plantilla Estructurada

```text
======================================================================
[PARTE FIJA - PREFIJO ESTATICO (~1.200 TOKENS) - CANDIDATO A CACHE]
======================================================================

Actua como un Ingeniero Senior de Triaje y Soporte Nivel 3 en una entidad FinTech regulada.

Tu objetivo principal es analizar el reporte de incidente del usuario, categorizar la anomalia, estimar la criticidad tecnica y extraer los elementos clave para derivar al equipo de ingenieria correspondiente.

---
TAXONOMIA DE CATEGORIAS DISPONIBLES:
1. INFRAESTRUCTURA_PAGOS: Fallas en transferencias interbancarias, rechazo de tarjetas o caidas de pasarela.
2. SEGURIDAD_AUTENTICACION: Bloqueos de cuenta por 2FA, intentos de acceso no reconocidos o sospechas de phishing.
3. ONBOARDING_KYC: Rechazos en verificacion biometrica o validacion documental.
4. UI_BUG_MENOR: Problemas cosmeticos en la interfaz web/movil que no impiden operar transacciones.
5. DISPUTA_COMERCIAL: Desacuerdos de comisiones o promociones no aplicadas.

---
REGLAS OBLIGATORIAS DE RESOLUCION:
- Regla 1: Si el usuario reporta retencion o perdida de dinero sin comprobante de confirmacion, la criticidad es "ALTA" y la categoria asignada debe ser siempre "INFRAESTRUCTURA_PAGOS".
- Regla 2: Si el usuario declara que no realizo una transaccion o que no puede ingresar tras recibir un SMS, clasificalo con criticidad "CRITICA" bajo "SEGURIDAD_AUTENTICACION".
- Regla 3: Si se mencionan errores visuales sin afectacion de balance, catalogar como "BAJA".

---
EJEMPLOS FEW-SHOT RESUELTOS:

Ejemplo 1:
- Input: "Envie $ 40.000 a otra cuenta por CVU, me debito el saldo pero al destinatario no le llego nada y ya pasaron 4 horas."
- Salida esperada:
  {"categoria": "INFRAESTRUCTURA_PAGOS", "criticidad": "ALTA", "requiere_bloqueo": false, "area_derivacion": "Squad Core Banking"}

Ejemplo 2:
- Input: "El boton de ver promociones se superpone con el saldo en pantalla en mi iPhone 15."
- Salida esperada:
  {"categoria": "UI_BUG_MENOR", "criticidad": "BAJA", "requiere_bloqueo": false, "area_derivacion": "Squad Front-end"}

---
DIRECTIVA DE RAZONAMIENTO:
Piensa paso a paso antes de darme la conclusion:
1. Extrae los hechos facticos reportados por el usuario, identificando montos, fechas y mensajes de error.
2. Contrasta los hechos contra las reglas obligatorias de triaje.
3. Determina la severidad operacional (BAJA / MEDIA / ALTA / CRITICA).
4. Genera la respuesta final formateada exclusivamente en sintaxis JSON plano sin bloques conversacionales adicionales.

ESTRUCTURA OBLIGATORIA DEL JSON:
{
  "categoria": "...",
  "criticidad": "...",
  "requiere_bloqueo": true/false,
  "area_derivacion": "..."
}

======================================================================
[PARTE VARIABLE - INYECTADA EN CADA UNA DE LAS 2.500 SOLICITUDES]
======================================================================
ID de Ticket: {{id_ticket}}
ID de Usuario: {{id_usuario}}
Fecha y Hora: {{timestamp_reporte}}
Descripcion del Incidente: {{cuerpo_reclamo}}
```

---

## PIEZA 2: Modelado del Flujo por Lotes (Message Batches API)

### Ciclo de Procesamiento y Marcas de Tiempo

```text
t0 --- 1. ENVIO DEL LOTE (Dispatch)
       - Ejecucion: POST /v1/messages/batches
       - Payload: Agrupacion de los 2.500 tickets en una sola peticion.
       - Respuesta sincrona inmediata:
         {
           "id": "msgbatch_ft01k992jklm02",
           "processing_status": "in_progress",
           "created_at": "2026-10-15T02:00:00Z"
         }
       |
       |   2. VENTANA DE PROCESAMIENTO (SLA asincrono de hasta 24 horas)
       |   - El procesamiento se ejecuta en segundo plano durante la madrugada.
       |   - No requiere agentes activos ni disponibilidad de respuesta inmediata en pantalla.
       |   - La aceptacion de este tiempo de espera otorga un 50% de descuento directo en tokens.
       |
t1 --- 3. RECUPERACION DE RESULTADOS (Retrieval)
       - Consulta de estado: GET /v1/messages/batches/msgbatch_ft01k992jklm02
         -> Respuesta: { "processing_status": "ended" }
       - Descarga de resultados: GET /v1/messages/batches/msgbatch_ft01k992jklm02/results
         -> Descarga de los 2.500 JSONs consolidados e inyeccion en el CRM a las 07:00 hs.
```

**Justificacion de tolerancia a la espera:**

El triaje masivo corresponde a la consolidacion del backlog operativo nocturno y reportes de guardia. Los incidentes se despachan en lote a las 02:00 AM y el equipo de soporte especializado toma los casos clasificados al inicio del turno matutino (08:00 AM). En consecuencia, un procesamiento asincrono con latencia de horas no afecta la experiencia del usuario y resulta viable para la operacion del negocio.

---

## PIEZA 3: Configuracion de Prefijo Cacheado (`cache_control`)

Para evitar reprocesar redundantemente las directivas del modelo en cada uno de los 2.500 tickets, se delimita el prefijo en el bloque `system` con el parametro de control efímero.

```json
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 250,
  "system": [
    {
      "type": "text",
      "text": "Actua como un Ingeniero Senior de Triaje y Soporte Nivel 3...\n[Aqui se incluye la totalidad del texto fijo (~1.200 tokens): rol, taxonomia, reglas, ejemplos y la orden de pensar paso a paso]",
      "cache_control": { 
        "type": "ephemeral" 
      }
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "ID de Ticket: {{id_ticket}}\nID de Usuario: {{id_usuario}}\nFecha y Hora: {{timestamp_reporte}}\nDescripcion del Incidente: {{cuerpo_reclamo}}"
    }
  ]
}
```

---

## PIEZA 4: Matriz de Costos y Eficiencia Financiera

### 1. Parametros y Supuestos del Modelo

- **Volumen total:** 2.500 incidentes.
- **Prefijo estatico (System Prompt cacheable):** 1.200 tokens por peticion.
- **Datos variables (User message):** 300 tokens por peticion.
- **Tokens de entrada por llamada:** 1.200 + 300 = 1.500 tokens.
- **Tokens de salida esperada:** 200 tokens por peticion (JSON estructurado).

**Volúmenes agregados:**
- Input Total: 2.500 * 1.500 tokens = 3.750.000 tokens = 3,75 MTok.
- Prefijo Total: 2.500 * 1.200 tokens = 3.000.000 tokens = 3,00 MTok.
- Variable Total: 2.500 * 300 tokens = 750.000 tokens = 0,75 MTok.
- Output Total: 2.500 * 200 tokens = 500.000 tokens = 0,50 MTok.

**Tarifas de Referencia (Claude 3.5 Sonnet):**
- Input base estandar: USD 3,00 / MTok
- Output base estandar: USD 15,00 / MTok
- Prompt Cache Read (Input con 90% de descuento): USD 0,30 / MTok

### 2. Matriz Comparativa de Costos

| Nivel de Optimizacion | Desglose de la Cuenta Aritmetica | Costo Resultante (USD) | Ahorro Financiero |
| :--- | :--- | :--- | :--- |
| **Fila 1: Linea Base (Sin optimizar)** | **Input:** 3,75 MTok * USD 3,00 = USD 11,25<br>**Output:** 0,50 MTok * USD 15,00 = USD 7,50 | **USD 18,75** | **0%** *(Base de comparacion)* |
| **Fila 2: Ahorro por Message Batches (-50%)** | Descuento del 50% sobre el total estandar:<br>USD 18,75 * 0,50 | **USD 9,37** | **- USD 9,38** *(50% de ahorro directo)* |
| **Fila 3: Ahorro por Prompt Caching (-90% en prefijo)** | **Prefijo en cache:** 3,00 MTok * USD 0,30 = USD 0,90<br>**Variable normal:** 0,75 MTok * USD 3,00 = USD 2,25<br>**Output normal:** 0,50 MTok * USD 15,00 = USD 7,50<br>**Total:** USD 0,90 + USD 2,25 + USD 7,50 | **USD 10,65** | **- USD 8,10** *(43,2% de ahorro)* |
| **Estrategia Combinada (Caching + Batch)** | **Subtotal con Caching:** USD 10,65<br>**Aplicacion del 50% Batch:** USD 10,65 * 0,50 | **USD 5,32** | **- USD 13,43** *(71,6% de ahorro total)* |

> **Nota Metodológica de Auditoría:**  
> Las tasas de descuento no son sumables aritméticamente (50% + 90% != 140%). El cómputo financiero se ejecuta en cascada: primero se liquida la tarifa reducida por lectura de prefijo en caché (USD 0,30 / MTok) junto al variable estándar, y sobre el balance resultante se aplica la bonificación del 50% por procesamiento por lotes asíncrono.
