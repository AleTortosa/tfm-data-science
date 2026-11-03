# TFM — Data Science / AI

Final Master's Project (Trabajo de Fin de Máster) for the Data Science/AI
master's degree at Evolve.

**Project:** SupermarketDemand — next-day demand forecasting for fresh produce,
to help reduce food waste from over-ordering and lost sales from stockouts.

This repository documents the incremental deliverables of the project.

## Structure

```
.
├── docs/entregas/
│   ├── 01_ideas_producto.md          # Entrega 1: three initial ideas
│   └── 02_datos_y_preparacion.md     # Entrega 2: data sources and Bronze/Silver/Gold
├── notebooks/
│   └── 01_pipeline_datos.ipynb       # Bronze -> Silver -> Gold, explained step by step
├── data/                             # See data/README.md (large data is not versioned)
│   └── sample/                       # Small real sample of the source
└── requirements.txt
```

## Quick start

Open `notebooks/01_pipeline_datos.ipynb` (locally with JupyterLab, or in Google Colab)
and run all cells.

```bash
pip install -r requirements.txt
jupyter lab notebooks/01_pipeline_datos.ipynb
```

## Status

🚧 In progress — Entrega 2 (data and preparation).