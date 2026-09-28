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
| 1. Supuestos de tu caso (celdas **amarillas con texto azul**, B4 a B15) | Cantidad de solicitudes, tokens de cada parte, precios del modelo, descuento de Batch y mínimo cacheable | **Sí** |
| 2. Tokens totales y chequeo del caché (fila 20) | Multiplicaciones a partir de tus supuestos, y si tu parte fija supera el mínimo cacheable | No |
| 3. Matriz de costos | Base, Solo Batch, Solo Caching, Caching + Batch | No |
| 4. Ahorro del caché sobre el prefijo | El "hasta 90%" aplicado a tu caso | No |
| Hoja **Precios** | Precios oficiales de Haiku 4.5, Sonnet 5 y Opus 5 | Solo para consultar |

## Paso a paso con tu caso

1. **Cantidad de solicitudes**: cuántas veces se ejecuta tu plantilla (ej.: 500 reseñas, 200 contratos).
2. **Tokens de la parte fija**: una estimación rápida es contar las palabras de tu parte fija y multiplicarlas por 3 (con la plantilla de Agencia Norte, 777 palabras dieron 2.261 tokens). Si tenés acceso al Playground de Claude, medila: mandá solo "Hola" con tu parte fija en System y restale 3 a los tokens de entrada.
3. **Tokens de la parte variable** y **de salida**: mismo método.
4. **Modelo**: elegí uno en la hoja **Precios** y copiá sus cinco precios (B9 a B13) y su mínimo cacheable (B15).
5. Mirá la **fila 20**: dice si tu parte fija supera el mínimo cacheable de tu modelo.
6. Revisá la matriz: las cuatro filas tienen que tener sentido (Base es la más cara, Caching + Batch la más barata).

> Si la fila 20 dice **NO**, tu parte fija es más corta que el mínimo y el caché no se activa. La planilla ya lo tiene en cuenta: las filas con caché valen lo mismo que sin caché. En tu PDF, decilo. Es un análisis correcto, no un error.

### Ejemplo: el mismo reporte con Haiku 4.5

Cambiá B8 a `Claude Haiku 4.5`, B9 a `1`, B10 a `5`, B11 a `1,25`, B12 a `2`, B13 a `0,1` y B15 a `4096` (en Sheets en inglés, los decimales van con punto). La fila 20 dice **NO**: 2.261 tokens es menos que 4.096. La base baja a US$ 1,87 (Haiku es más barato), pero el caché no aporta nada: con Batch queda en US$ 0,93.

## Qué copiar a tu PDF

- La tabla de la zona 3 (con tus números): son las filas separadas que pide la rúbrica.
- Debajo, tus supuestos escritos (zona 1): es lo que hace verificable la cuenta.
- La cuenta de la fila Solo Batch y la de la fila Solo Caching, cada una en su renglón.

## Ejemplo cargado (Agencia Norte, Claude Sonnet 5)

| Escenario | Total | Ahorro |
|---|---:|---:|
| Base, sin optimizar | US$ 3,74 | 0% |
| Solo Batch | US$ 1,87 | 50,0% |
| Solo Caching (5 minutos) | US$ 1,30 | 65,2% |
| Caching (1 hora) + Batch | US$ 0,65 | 82,6% |

Supuestos, medidos en el Playground de Claude: 600 consultas · parte fija 2.261 tokens · parte variable 177 · salida 135 (promedios de los correos de Martina y Julián).
