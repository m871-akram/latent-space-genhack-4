Latent-Space [16] — GenHack Week 4 Wrap-up

Overview
- This repository contains materials for the GenHack project by team LATENT-SPACE [16], focusing on Urban Heat Islands (UHI).
- The primary working artifact is a Jupyter notebook that explores relationships between NDVI (Sentinel-2/3), ERA5-Land temperature, weather stations, wind, and land cover to quantify UHI effects and error drivers.
- The analysis is primarily exploratory/research-oriented and is not packaged as a library or CLI app.

Stack Detection
- Language: Python (Jupyter Notebook)
- Primary artifact: `week{4}_team{16}.ipynb`
- Notable libraries used (from the notebook imports):
  - Core scientific stack: numpy, pandas, xarray
  - Geospatial: geopandas, rasterio, shapely, cartopy
  - Plotting: matplotlib, seaborn
  - Statistics/ML: scipy, scikit-learn, statsmodels
  - Deep learning: torch (PyTorch)
  - Utilities: tqdm, pathlib
- Package manager: not specified in the repo; Pip/Conda/Mamba are likely options. Geospatial deps (GDAL/GEOS/PROJ) are easier via Conda.

Project Structure
- Root directory contents:
  - `week{4}_team{16}.ipynb` — main analysis notebook
  - `week{4}_team{16}.pdf` — exported slides/report for week 4
  - `week{3}team{16}.pdf` — prior week report
  - `week{2}_team{16}.mov`, `week{2}_team{16}.pdf` — week 2 materials
  - `week{1}_team{16}.mp4`, `week{1}_team{16}.pdf` — week 1 materials
  - `Screen Recording 2025-12-01 at 12.53.32.mov` — screen recording
  - (No `requirements.txt` or environment files at present)

Data Paths and Inputs
- The notebook references local datasets via hard-coded absolute paths, for example:
  - `BASE_DIR = 
    /Users/mohammedakramlrhorfi/Library/Mobile Documents/com~apple~CloudDocs/hackathon_data`
  - Google Drive shortcuts for Sentinel data under `.../data_genhack/...`
- These paths are user-specific and will need to be adapted for new environments.
- Recommended approach (TODO): refactor the notebook to read base paths from environment variables or a config cell, e.g. `BASE_DIR`, `SENTINEL2_NDVI_PATH`, `SENTINEL3_NDVI_DIR`, `ERA5_LAND_DIR`, `GADM_EUROPE_PATH`.

Requirements
- Python 3.9–3.11 recommended (exact version not specified; pick one compatible with GeoPandas/Rasterio and PyTorch for your platform).
- OS: macOS, Linux, or Windows with WSL. For geospatial stacks, Conda/Mamba is strongly recommended.
- Key Python packages:
  - `numpy`, `pandas`, `xarray`
  - `geopandas`, `rasterio`, `shapely`, `cartopy`
  - `matplotlib`, `seaborn`
  - `scipy`, `scikit-learn`, `statsmodels`
  - `torch`, `tqdm`

Setup (Option A: Conda/Mamba — recommended)
1) Create and activate an environment (adjust Python version as needed):
```
mamba create -n uhi python=3.11 -y
mamba activate uhi
```
2) Install geospatial dependencies via Conda channels (conda-forge):
```
mamba install -c conda-forge geopandas rasterio shapely cartopy pyproj fiona gdal -y
```
3) Install the remaining packages:
```
python -m pip install numpy pandas xarray matplotlib seaborn scipy scikit-learn statsmodels tqdm
```
4) Install PyTorch appropriate for your platform (CPU-only example shown):
```
python -m pip install torch --index-url https://download.pytorch.org/whl/cpu
```

Setup (Option B: Pip only — may require system GDAL/GEOS/PROJ)
- On macOS (Homebrew):
```
brew install gdal geos proj
python -m venv .venv && source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install numpy pandas xarray matplotlib seaborn scipy scikit-learn statsmodels tqdm
python -m pip install shapely pyproj fiona rasterio geopandas cartopy
python -m pip install torch --index-url https://download.pytorch.org/whl/cpu
```

How to Run
- Using JupyterLab or VS Code is recommended.
1) Launch JupyterLab:
```
jupyter lab
```
2) Open `week{4}_team{16}.ipynb` and execute cells from top to bottom.

Environment Variables and Configuration
- None are currently required by the repo; the notebook uses hard-coded absolute paths.
- Recommended (TODO): support the following variables to avoid hard-coding user-specific paths:
  - `BASE_DIR` — base folder for common datasets
  - `GADM_EUROPE_PATH`
  - `ERA5_LAND_DIR`
  - `SENTINEL2_NDVI_PATH`
  - `SENTINEL3_NDVI_DIR`
- Example usage in notebook (pseudo-code):
```
import os
from pathlib import Path
BASE_DIR = Path(os.environ.get("BASE_DIR", "/path/to/default"))
```

Scripts and Entry Points
- There are no Python modules or CLI scripts in the repo; the main entry point is the Jupyter notebook.

Tests
- There are no automated tests in this repository.

Reproducibility Notes
- Results depend on external datasets and local absolute paths; ensure you have the data available and paths configured.
- Some computations may be resource-intensive (geospatial raster operations, ML training).
- Current local date/time for this README generation: 2025-12-04 12:19 (local).

License
- No license file is present.

Maintainers
- Team: LATENT-SPACE [16]
- If you are not part of the original project team, please adapt data paths and environment setup accordingly.
