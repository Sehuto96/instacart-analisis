# Reporte de calidad de datos: Instacart

Diagnóstico de los cinco archivos crudos de `data/raw/` y decisiones de limpieza.

- Exploración (Lección 1): [`notebooks/data_quality.ipynb`](../notebooks/data_quality.ipynb)
- Limpieza (Lección 2): [`notebooks/pandas_labs.ipynb`](../notebooks/pandas_labs.ipynb)
- Bitácora de problemas: `data/processed/data_quality_log.csv`
- Informe de cambios del pipeline: `data/processed/cleaning_report.csv`

## 1. Inventario de los datos crudos

| Tabla | Archivo | Filas | Columnas | Clave | Nulos | Duplicados |
| --- | --- | ---: | ---: | --- | ---: | ---: |
| `orders` | `instacart_orders.csv` | 478 967 | 6 | `order_id` | 28 819 | 15 |
| `order_prods` | `order_products.csv` | 4 545 007 | 4 | `order_id` + `product_id` | 836 | 0 |
| `products` | `products.csv` | 49 694 | 4 | `product_id` | 1 258 | 0 |
| `aisles` | `aisles.csv` | 134 | 2 | `aisle_id` | 0 | 0 |
| `departments` | `departments.csv` | 21 | 2 | `department_id` | 0 | 0 |

Los archivos usan `;` como separador, así que se leen con `pd.read_csv(..., sep=';')`.

Las relaciones entre tablas no tienen valores huérfanos: todo producto pedido existe en el catálogo y todo pasillo y departamento existe en su tabla.

## 2. Problemas encontrados y decisiones

| # | Tabla | Problema | Evidencia | Decisión | Justificación |
| --- | --- | --- | ---: | --- | --- |
| 1 | `orders` | Filas duplicadas exactas | 15 | **Eliminar** | Todas ocurren el miércoles a las 2 a. m.: es un fallo del sistema, no pedidos reales |
| 2 | `orders` | Columnas enteras leídas como `float64` / `int64` | 23,0 MB | **Corregir** con `astype` | Tipos más pequeños: la memoria pasa de 23,0 a 7,2 MB |
| 3 | `orders` | Nulos en `days_since_prior_order` | 28 819 (28 817 tras quitar duplicados) | **Conservar** (`Int16`) | Cada nulo es un primer pedido: no existe un pedido anterior. Rellenar con 0 falsearía la espera media |
| 4 | `products` | Nulos en `product_name` | 1 258 | **Corregir** con `'Unknown'` | El producto existe (pasillo 100 y departamento 21, `missing`) y no se puede borrar |
| 5 | `products` | Espacios dobles en los nombres | 403 | **Corregir** | Se reemplazan por uno solo con `.str.replace(r'\s+', ' ')` |
| 6 | `products` | Espacio invisible `\xa0` en los nombres | 16 | **Corregir** | Se cambia por un espacio normal en un paso propio |
| 7 | `products` | Mayúsculas sin estandarizar | — | **Corregir** con `.str.title()` | Nombres comparables entre sí |
| 8 | `products` | Duplicados implícitos (mismo nombre, distinto `product_id`) | 141 nombres, 280 productos | **Conservar y documentar** | 268 de los 280 aparecen en pedidos; borrarlos dejaría pedidos sin producto. Se agrupa por `product_id` |
| 9 | `order_prods` | Nulos en `add_to_cart_order` | 836 | **Conservar** (`Int16`) | Ver Reto 2, punto 3 |
| 10 | `order_prods` | Tipos enteros de 64 bits | 145,4 MB | **Corregir** con `astype` | La memoria pasa de 145,4 a 54,5 MB |
| 11 | `departments` | Valor centinela `missing` | 1 | **Conservar** como texto | `isna()` no lo detecta; si se convierte en nulo, `groupby` lo descartaría en silencio |
| 12 | `orders` | Pedidos sin productos en `order_prods` | 28 906 (6 %) | **Conservar y documentar** | Se usa `how='left'` al combinar tablas (Lección 4) para no perderlos sin aviso |

## 3. Resultado de la limpieza

| Tabla | Filas antes | Filas después | Nulos antes | Nulos después | Memoria antes | Memoria después |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| `orders` | 478 967 | 478 952 | 28 819 | 28 817 | 23,0 MB | 7,2 MB |
| `products` | 49 694 | 49 694 | 1 258 | 0 | 3,1 MB | 3,1 MB |
| `order_prods` | 4 545 007 | 4 545 007 | 836 | 836 | 145,4 MB | 54,5 MB |

La limpieza se ejecuta como un pipeline de funciones encadenadas con `pipe` y termina con una validación automática (`assert`) de estas reglas:

- `order_id` es único en `orders`.
- `order_hour_of_day` está entre 0 y 23.
- Ningún producto queda sin nombre ni con espacios invisibles.
- `reordered` solo contiene 0 y 1.
