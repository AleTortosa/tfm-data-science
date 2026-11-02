# Entrega 2 — Datos y preparación

**Proyecto elegido: SupermarketDemand** — predicción de la demanda de productos frescos para reducir el desperdicio alimentario en supermercados de barrio.

Esta entrega muestra que la idea se puede construir con datos reales y deja un dataset Gold listo para la fase de Machine Learning. El proceso completo, explicado paso a paso y con sus resultados, está en el notebook [`notebooks/01_pipeline_datos.ipynb`](../../notebooks/01_pipeline_datos.ipynb).

## 1. Qué datos necesita el producto

**Decisión que se quiere mejorar:** cuánto pedir mañana de cada producto fresco. Pedir de más genera desperdicio; pedir de menos genera roturas de stock y ventas perdidas.

- **Una observación** = un producto, en una tienda, en un día.
- **Imprescindible:** venta diaria del producto y si hubo rotura de stock (sin esto no se sabe si una venta baja es poca demanda o falta de producto).
- **Deseable:** promociones, festivos, clima, y la venta hora a hora para ver cuándo se agota el producto.
- **Granularidad e histórico:** diaria. Con 90 días por serie se puede captar el patrón semanal, pero no la estacionalidad anual (ver limitaciones).

**Variable a estimar:** la venta del día siguiente, `target_next_day_sales`, por tienda y producto.

## 2. Fuentes reales y viabilidad

### Fuente principal: FreshRetailNet-50K

| | |
|---|---|
| Qué es | Dataset académico de venta de productos perecederos de la plataforma Dingdong (retail de frescos en China) |
| Cobertura | 898 tiendas, 18 ciudades, unos 863–865 productos perecederos (el artículo dice 863 y el README 865), 50.000 series de 90 días |
| Contenido | Venta diaria y horaria, estado de stock hora a hora (con unas 20 % de roturas reales), descuento, festivo, actividad promocional y clima (precipitación, temperatura, humedad, viento) |
| Acceso | Hugging Face: `load_dataset("Dingdong-Inc/FreshRetailNet-50K")` (reproducible, ver `bronze`) |
| Licencia | CC BY 4.0 |
| Referencia | Wang et al. (2025), arXiv:2505.16319 |

**Por qué esta fuente:** trae anotadas las roturas de stock, que es justo lo que distingue "demanda baja" de "producto agotado". Pocos datasets públicos lo tienen.

**Estabilidad y actualización:** es una publicación académica cerrada, no se actualiza, lo cual da estabilidad (los resultados son reproducibles) pero impide trabajar con datos recientes.

**Alternativa si dejara de estar disponible:** Corporación Favorita (Kaggle, ventas diarias de varios años con la columna `perishable` en `items.csv`) o M5 Walmart (categoría FOODS). Ambas pierden la anotación de roturas de stock.

### Fuentes españolas valoradas y descartadas

Se buscaron datos de supermercados españoles y **no se encontró ningún dataset público de ventas por tienda**, porque son datos comerciales de las cadenas. Lo que sí existe no sirve para este caso:

- *Panel de Consumo Alimentario (MAPA):* consumo de hogares agregado a nivel nacional o regional, sin detalle de tienda.
- *Mercabarna / Mercamadrid:* toneladas y precios en mercados mayoristas, no demanda de tienda; lo localizado son publicaciones anuales en PDF.
- *Precios de supermercados online (comparadores o scrapers):* son precios, no ventas, y el scraping plantea problemas de condiciones de uso.

### Idea descartada tras la comprobación de datos: ParkingPredict (Valencia)

El portal de datos abiertos del Ayuntamiento publica la ubicación y capacidad de los parkings, no su ocupación. Se revisó un archivo histórico de terceros con instantáneas cada 15 minutos y, en las fechas inspeccionadas (1 de marzo de 2020, 2023 y 2024), la columna de plazas libres valía -1 en todas las filas en 2020 y no existía en 2023 ni 2024, de modo que no hay variable a predecir. Se descarta por falta de la variable objetivo.

## 3. Uso, privacidad y datos personales

- **Licencia:** CC BY 4.0 permite su uso, incluso comercial, con atribución. Se cita la fuente y el artículo en `data/README.md`. La propia ficha indica que cada usuario debe comprobar que la licencia es adecuada para su finalidad; para un trabajo académico como este lo es.
- **Datos personales:** el esquema solo contiene identificadores numéricos de ciudad, tienda y producto, cifras de venta y variables meteorológicas. No hay datos de clientes. No se necesita minimización adicional.
- **En el repositorio** solo se sube una muestra de 20 series; los datos completos se regeneran ejecutando el notebook.

## 4. Del dato bruto al dato utilizable

```
Hugging Face ──bronze──▶ data/raw/*.parquet ──silver──▶ data/silver/*_silver.parquet ──gold──▶ data/gold/*_gold.parquet
                         (tal cual llega)       (limpio, 1 fila = tienda-producto-día)       (features + objetivo)
```

### Bronze

La sección Bronze del notebook descarga los splits `train` y `eval` y los guarda **sin modificar** en Parquet, con un `bronze_manifest.json` (fuente, licencia, fecha de descarga y filas). También genera `data/sample/freshretailnet_sample.parquet`: 20 series completas elegidas al azar con semilla fija.

### Silver

Una fila por tienda, producto y día, con tipos correctos. Cada regla cuenta lo que descarta y lo guarda en un informe JSON:

| Regla | Qué hace |
|---|---|
| Esquema | Falla si falta alguna de las 19 columnas esperadas |
| Arrays horarios | Descarta filas cuyos arrays `hours_sale` o `hours_stock_status` no tengan 24 valores o contengan nulos |
| Nulos | Descarta filas sin tienda, producto, fecha o venta |
| Duplicados | Una sola fila por tienda-producto-día (se conserva la primera) |
| Ventas negativas | Se descartan |
| Tipos | Fechas a `datetime`, identificadores a entero, banderas a entero nullable, medidas a `float32` |
| Aplanado horario | De los 24 valores por hora se derivan: hora pico de ventas, horas sin stock en el día y horas sin stock entre las 6 y las 21 h. El detalle horario sigue en Bronze |

**Controles de calidad** (se informan, no filtran): coincidencia entre la venta diaria y la suma de las horarias; coincidencia entre `stock_hour6_22_cnt` de la fuente y el recuento propio; porcentaje de descuentos fuera de (0, 1]; nulos por columna.

### Gold

**Cada fila** es un producto en una tienda en un día `d`, con la información disponible al cierre de ese día para predecir la venta del día `d+1`.

| Grupo | Columnas |
|---|---|
| Identificación | `city_id`, `store_id`, `management_group_id`, `product_id`, `category_1`, `category_2`, `category_3`, `date` |
| Estado del día d | `sales`, `stockout_share_6_21` (fracción de horas 6–21 sin stock), `peak_hour`, `discount`, `holiday`, `activity`, `precipitation`, `temperature`, `humidity`, `wind_level` |
| Histórico | `sales_lag_1`, `sales_lag_7`, `sales_roll_mean_7` (media de los 7 días anteriores, con un mínimo de 3 observaciones), `stockout_share_lag_1` |
| Conocido de antemano sobre d+1 | `discount_next_day`, `holiday_next_day`, `activity_next_day` (una tienda conoce con antelación sus promociones y el calendario) |
| Calendario | `dow`, `is_weekend`, `month` |
| **Objetivo** | `target_next_day_sales`, y `target_next_day_stockout_share` para saber cuánto está "censurado" ese objetivo |

**Decisiones de diseño que evitan errores silenciosos:**

- Los desfases se calculan sobre una rejilla diaria completa: si falta un día, el desfase queda en `NaN` en lugar de tomar el valor de otro día.
- Ninguna feature usa información posterior al día `d`, salvo las `*_next_day`, que son planificadas.
- Se eliminan las filas sin objetivo (el último día de cada serie y el día anterior a un hueco de fechas).
- Los `NaN` de los primeros días (sin histórico) se mantienen y se dejan al modelo.

**Criterio de éxito (se aplicará en la Entrega 3):** el modelo debe batir a los baselines ingenuos `sales_lag_1`, `sales_lag_7` y `sales_roll_mean_7` en **WAPE** (error absoluto total sobre ventas totales) y MAE, con una **partición temporal** (entrenar con las fechas más antiguas y validar con las últimas dos semanas, nunca al azar). Los resultados se desglosan entre días sin rotura de stock (`target_next_day_stockout_share = 0`) y con rotura, porque en estos últimos la venta observada es menor que la demanda real.

### Resultado de la ejecución sobre los datos reales

Las cifras salen de la ejecución del notebook (ver sus celdas de salida):

| Medida | Valor |
|---|---|
| Filas en Bronze | 4.500.000 en `train` y 350.000 en `eval` (este último no se usa todavía) |
| Filas descartadas por regla | Ninguna: 0 en arrays horarios incompletos, nulos, duplicados y ventas negativas. La fuente ya viene limpia en estos aspectos |
| Series y filas en Silver | 50.000 series, 4.500.000 filas |
| Filas y columnas en Gold | 4.450.000 filas, 30 columnas (se pierde el último día de cada serie, que no tiene "día siguiente") |
| Rango de fechas | 28/03/2024 a 25/06/2024 en Silver (90 días por serie); hasta 24/06/2024 en Gold |
| Roturas de stock en el objetivo | El 44,2 % de las filas tiene alguna hora sin stock el día que se quiere predecir |

**Qué dicen los controles de calidad** (en el notebook, sin filtrar nada):

- La venta diaria coincide con la suma de las ventas horarias en el 100 % de las filas.
- El resumen `stock_hour6_22_cnt` de la fuente coincide en el 100 % con el recuento propio de horas sin stock entre las 6 y las 21 h. Esto confirma cómo se calcula esa ventana y que `hours_stock_status = 1` equivale a "sin stock".
- El 99,64 % de los descuentos está en el rango (0, 1]. El 0,36 % restante (unas 16.000 filas) queda fuera y no se ha eliminado; se revisará en la fase de modelado.
- No hay nulos en ninguna columna de Silver.
- Los nulos de Gold son los esperados: `sales_lag_1` y `stockout_share_lag_1` 1,1 % (primer día de cada serie), `sales_lag_7` 7,9 % (primera semana) y `sales_roll_mean_7` 3,4 % (primeros tres días, sin el mínimo de datos).

## 5. Riesgos y limitaciones

- **No hay datos de desperdicio y las ventas están normalizadas.** El dataset recoge ventas y stock, no merma, y la ficha indica que las ventas están multiplicadas por un coeficiente, así que no son unidades reales. El proyecto estima la demanda como paso previo a decidir cuánto pedir; el ahorro en desperdicio, en euros o en kilos, no se medirá directamente.
- **Casi la mitad del objetivo está censurado.** En el 44,2 % de las filas hubo alguna hora sin stock el día a predecir, así que la venta observada subestima la demanda real. Por eso la evaluación se desglosará entre días con y sin rotura de stock.
- **Solo 90 días por serie.** No se puede captar estacionalidad anual ni el efecto de festivos poco frecuentes.
- **Contexto distinto del objetivo.** Son datos de una plataforma china de retail fresco, no de supermercados de barrio españoles; que el patrón de demanda sea transferible es una hipótesis, no un resultado. No existen datos públicos equivalentes en España.
- **Si la fuente dejara de estar disponible:** se conservan el manifiesto de Bronze y la muestra en el repositorio, y el pipeline se adaptaría a Favorita (perecederos) o M5, aceptando perder la anotación de roturas de stock.
