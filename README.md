# instacart-analisis

Análisis de los datos de pedidos de Instacart con Python y pandas, desarrollado en la materia Ciencia de datos con Python (Análisis y Desarrollo de Software, SENA).

**Pregunta orientadora:** ¿cómo preparar y estructurar los datos de Instacart para obtener información confiable sobre los hábitos de compra?

## Actividades

| Actividad | Contenido | Cuaderno |
| --- | --- | --- |
| Entorno y cuaderno | Estructura del proyecto, Git, entorno virtual y primer cuaderno | `notebooks/00_entorno.ipynb` |
| GA1-220501094-01-AA1-EV04 | Exploración de datos de Instacart con Python y Pandas | `notebooks/Exploracion_de_datos_de_Instacart_con_Python_y_Pandas.ipynb` |
| GA1-220501094-01-AA1-EV06 | Limpieza, análisis y estructuración de datos con Pandas | `notebooks/data_quality.ipynb`, `notebooks/pandas_examples.ipynb`, `notebooks/pandas_labs.ipynb` |

### EV06 por lección

| Lección | Tema | Dónde está |
| --- | --- | --- |
| 1 | Calidad de datos: ficha técnica, claves, huérfanos y bitácora de problemas | `data_quality.ipynb` y `reports/data_quality.md` |
| 2 | Limpieza y estandarización: duplicados, tipos, nulos, texto y pipeline con informe | `pandas_labs.ipynb` (Laboratorio 2 y Reto 2) |
| 3 | Filtrado, ordenamiento y agrupación: perfil de hábitos de compra | `pandas_labs.ipynb` (Laboratorio 3 y Reto 3) |
| 4 | Combinación de datasets: tabla maestra y análisis por producto | `pandas_labs.ipynb` (Laboratorio 4 y Reto 4) |

Los ejemplos de la explicación de las lecciones 2 a 4 están en `pandas_examples.ipynb`.

## Estructura

```
instacart-analisis/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/              # los 5 CSV originales (no se suben a GitHub)
│   └── processed/        # archivos generados por los cuadernos
├── notebooks/
│   ├── 00_entorno.ipynb
│   ├── Exploracion_de_datos_de_Instacart_con_Python_y_Pandas.ipynb
│   ├── data_quality.ipynb
│   ├── pandas_examples.ipynb
│   └── pandas_labs.ipynb
└── reports/
    └── data_quality.md
```

## Datos

Los cinco archivos de Instacart van en `data/raw/` y usan `;` como separador:

| Archivo | Filas | Contenido |
| --- | ---: | --- |
| `instacart_orders.csv` | 478 967 | Un pedido por fila |
| `order_products.csv` | 4 545 007 | Un producto dentro de un pedido |
| `products.csv` | 49 694 | Catálogo de productos |
| `aisles.csv` | 134 | Pasillos |
| `departments.csv` | 21 | Departamentos |

Los CSV no están en el repositorio por su tamaño (`order_products.csv` pesa 85,9 MB). Hay que copiarlos en `data/raw/` antes de ejecutar los cuadernos.

## Cómo ejecutar

Requisitos: Python 3.14 y VS Code o JupyterLab. En PowerShell, desde la carpeta del proyecto:

1. Crear y activar el entorno virtual:

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

2. Instalar las dependencias:

   ```powershell
   pip install -r requirements.txt
   ```

3. Copiar los cinco CSV en `data/raw/`.
4. Abrir los cuadernos de `notebooks/`, elegir el kernel `.venv` y ejecutar **Restart → Run All**, en este orden:
   1. `data_quality.ipynb`
   2. `pandas_examples.ipynb`
   3. `pandas_labs.ipynb`

Cada cuaderno carga y limpia los datos al inicio, así que también se puede ejecutar por separado.

## Archivos generados

Los cuadernos guardan sus resultados en `data/processed/`:

| Archivo | Lección | Contenido |
| --- | --- | --- |
| `data_quality_log.csv` | 1 | Bitácora de problemas de calidad y decisiones |
| `cleaning_report.csv` | 2 | Filas, nulos y memoria antes y después de cada paso de limpieza |
| `segment_hour_profile.csv` | 3 | Porcentaje de pedidos por franja horaria en cada segmento de cliente |
| `instacart_master.parquet` | 4 | Tabla maestra con las cinco tablas unidas (4 545 007 filas, 14 columnas). No se sube a GitHub: se regenera al ejecutar el cuaderno |
| `department_summary.csv` | 4 | Artículos y tasa de recompra por departamento |
| `product_profile.parquet` | 4 | Ficha de 45 573 productos: veces pedido, clientes, tasa de recompra y posición media |
| `department_leaders.csv` | 4 | Producto con mayor tasa de recompra de cada departamento |

Los archivos `.parquet` se leen con `pd.read_parquet()` y necesitan `pyarrow`.

## Ramas y versiones

- `main`: versión estable.
- `develop`: integración de las actividades terminadas.
- `feature/pandas-data-processing`: desarrollo de la EV06, fusionada con `develop` al terminar.

Commits de la EV06:

| Avance | Mensaje |
| --- | --- |
| Lección 2 | `feat: complete lesson 2 data cleaning` |
| Lección 3 | `feat: complete lesson 3 data analysis` |
| Lección 4 | `feat: complete lesson 4 data merging` |
| Documentación | `docs: update README and processing report` |
