# Guía 3 — La calculadora de costos

La planilla [`calculadora-costos-claude.xlsx`](./calculadora-costos-claude.xlsx) hace las cuentas de la Guía 2 por vos. Viene cargada con el ejemplo de Agencia Norte; cambiás los supuestos por los de tu caso y la matriz de costos se actualiza sola. Es la **Pieza 4** de tu pre-entrega.

## Cómo abrirla en Google Sheets

1. Descargá el archivo (en GitHub: clic en el archivo > botón **Download raw file**).
2. En Google Drive: **Nuevo** > **Subir archivo** > elegí la planilla.
3. Abrila con doble clic > **Abrir con Hojas de cálculo de Google**.
4. **Archivo** > **Guardar como Hojas de cálculo de Google**. Trabajá sobre esa copia.

También funciona en Excel, tal cual.

## Qué se edita y qué no

| Zona | Qué es | ¿Se edita? |
|---|---|---|
| 1. Supuestos de tu caso (celdas **amarillas con texto azul**) | Cantidad de solicitudes, tokens de cada parte, precios del modelo, descuento de Batch | **Sí** |
| 2. Tokens totales | Multiplicaciones a partir de tus supuestos | No |
| 3. Matriz de costos | Base, Solo Batch, Solo Caching, Caching + Batch | No |
| 4. Ahorro del caché sobre el prefijo | El "hasta 90%" aplicado a tu caso | No |
| Hoja **Precios** | Precios oficiales de Haiku 4.5, Sonnet 5 y Opus 5 | Solo para consultar |

## Paso a paso con tu caso

1. **Cantidad de solicitudes**: cuántas veces se ejecuta tu plantilla (ej.: 500 reseñas, 200 contratos).
2. **Tokens de la parte fija**: una estimación rápida es contar las palabras de tu parte fija y multiplicarlas por 2. Si tenés acceso al Playground de Claude, usá el número real que muestra.
3. **Tokens de la parte variable** y **de salida**: mismo método.
4. **Modelo**: elegí uno en la hoja **Precios** y copiá sus cinco precios en las celdas amarillas.
5. Revisá la matriz: las cuatro filas tienen que tener sentido (Base es la más cara, Caching + Batch la más barata).

> Chequeo importante: si tus tokens de la parte fija están **por debajo del mínimo cacheable** de tu modelo (hoja Precios), el caché no se activa. En ese caso, la fila de caching no aplica a tu caso: decilo en tu PDF. Es un análisis correcto, no un error.

## Qué copiar a tu PDF

- La tabla de la zona 3 (con tus números): son las filas separadas que pide la rúbrica.
- Debajo, tus supuestos escritos (zona 1): es lo que hace verificable la cuenta.
- La cuenta de la fila Solo Batch y la de la fila Solo Caching, cada una en su renglón.

## Ejemplo cargado (Agencia Norte, Claude Sonnet 5)

| Escenario | Total | Ahorro |
|---|---:|---:|
| Base, sin optimizar | US$ 3,06 | 0% |
| Solo Batch | US$ 1,53 | 50,0% |
| Solo Caching (5 minutos) | US$ 1,44 | 52,8% |
| Caching (1 hora) + Batch | US$ 0,72 | 76,4% |

Supuestos: 600 consultas · parte fija 1.500 tokens · parte variable 300 · salida 150.
