# Ecodata: de un export de pedidos a un informe en PDF

Una sola herramienta que hace la cadena completa: **limpia → analiza → grafica → arma el PDF**.

## Instalar (una vez)
    pip install -r requirements.txt

## Usar
    python ecodata.py pedidos.csv --cliente "Nombre del cliente"

Genera `informe_<cliente>.pdf` y un CSV con los datos ya limpios.

Opciones:
- `--salida informe.pdf`: nombre del PDF.
- `--recomendaciones recs.txt`: tus recomendaciones, una por línea con el formato `Título | detalle`. Reemplazan el borrador automático.
- `--final`: quita la marca "BORRADOR" (úsalo cuando ya revisaste todo).
- `--mapeo-columnas`: solo muestra qué columnas detectó, sin generar nada. Úsalo primero con cada cliente nuevo.

## Flujo de trabajo recomendado
1. Corres con `--mapeo-columnas` para ver si reconoció las columnas.
2. Corres normal y revisas el PDF borrador y el CSV limpio.
3. Escribes tus recomendaciones en `recs.txt` y corres otra vez, o usas `--final`.
4. Mandas el PDF por WhatsApp o Instagram.

## Qué limpia solo
- Excluye pedidos cancelados, reembolsados o anulados.
- Quita filas duplicadas exactas.
- Normaliza códigos postales (quita apóstrofes, espacios y sufijos como `-0000`).
- Lee fechas mezcladas (`2026-03-14`, `14/03/2026`, con o sin hora).
- Rellena fecha y zona en las filas de un mismo pedido (Shopify solo las pone en la primera).
- Quita símbolos de moneda de los precios (`$349.00` → `349`).
- Descarta filas sin fecha, sin zona o con cantidad o precio inválidos, y lo reporta.

## Qué entrega el PDF
Resumen, mapa de calor por zona (esquema, no mapa geográfico), categorías por zona,
margen por categoría (solo si el archivo trae columna de costo), tres zonas principales
y recomendaciones. Las recomendaciones automáticas son un **borrador basado en reglas**:
siempre revísalas y ajústalas con tu criterio antes de enviar.

## Límites que conviene saber
- **Las columnas están en una lista de nombres posibles** (`ALIASES` al inicio de `ecodata.py`).
  Los nombres de Shopify están probados con datos de ejemplo; los de Mercado Libre y otros
  se basan en nombres típicos, pero **verifícalos con un export real** y agrega los que falten.
- Si dos líneas idénticas de un mismo pedido aparecen en filas separadas, se tratan como duplicado.
- La zona sale del código postal (o colonia/ciudad si no hay CP). Para un mapa geográfico real
  haría falta una tabla de CP → coordenadas.
- El margen solo descuenta el costo del producto, no envíos, comisiones de la plataforma ni impuestos.
- No es contabilidad ni asesoría fiscal.

## Datos de ejemplo
`python generar_datos_ejemplo.py` crea `datos_ejemplo_shopify.csv` (ficticio y desordenado)
para practicar. `informe_ejemplo.pdf` es el resultado de correrlo.
