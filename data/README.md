# Datos

Los datos grandes **no se suben al repositorio**; se regeneran ejecutando
`notebooks/01_pipeline_datos.ipynb`.

| Carpeta | Capa | Contenido | ¿En Git? |
|---|---|---|---|
| `raw/` | Bronze | Parquet tal como llega de la fuente (`train.parquet`, `eval.parquet`) + `bronze_manifest.json` | No |
| `sample/` | Bronze (muestra) | 20 series tienda-producto completas (90 días cada una), elegidas al azar con semilla 42 | **Sí** |
| `silver/` | Silver | Una fila por tienda-producto-día, tipado y limpio + informe JSON de lo descartado | No |
| `gold/` | Gold | Dataset de modelado: features y objetivo | No |

## Cómo obtenerlos

Ejecutar el notebook completo. La primera celda de Bronze descarga el dataset desde
Hugging Face (puede tardar y ocupar varios cientos de MB) y crea la muestra.
Si falta memoria, en la celda de configuración pon, por ejemplo, `STORES = [0, 1, 2, 3]`.

## Fuente

FreshRetailNet-50K, Dingdong. Licencia CC BY 4.0.
<https://huggingface.co/datasets/Dingdong-Inc/FreshRetailNet-50K>
Wang, Y. et al. (2025). *FreshRetailNet-50K: A Stockout-Annotated Censored Demand Dataset for Latent Demand Recovery and Forecasting in Fresh Retail.* arXiv:2505.16319.