# ls-lrsi-aranayake

Location Specific Landslide Risk Scoring Index (LS-LRSI) for Aranayake
Divisional Secretariat Division, Kegalle District, Sri Lanka.

## What this is

A landslide risk score, on a 30 m grid, for the Aranayake area, built from
real terrain, vegetation, rainfall, infrastructure, soil, and historical
landslide data. Built for DAS7003 Geospatial Analysis, PRAC1.

## Project structure

    project/
    ├── README.md
    ├── data/
    │   ├── raw/            # exactly as downloaded, untouched
    │   └── processed/      # reprojected, resampled to a common 30 m grid
    ├── notebooks/
    │   ├── 01_data_acquisition.ipynb   # loads and checks every raw file
    │   ├── 02_preprocessing.ipynb      # reprojection, resampling, feature engineering
    │   ├── 03_eda.ipynb                # maps, plots, correlation analysis
    │   ├── 04_index_construction.ipynb # scoring pipeline
    │   └── 05_validation.ipynb         # cross-check against NBRO zonation and inventory
    └── outputs/
        ├── figures/
        └── ls_lrsi_risk_map.tif

## How to run

1. Install Python 3.10 or newer.
2. Install the libraries:

       pip install numpy pandas matplotlib scipy rasterio geopandas rioxarray requests jupyter

3. Download the raw data (see below) and place each file in `data/raw/`
   using the folder names listed in the table.
4. Run the notebooks in order, 01 through 05. Each one reads from either
   `data/raw/` or `data/processed/` and writes its results forward to the
   next notebook, so they need to run in sequence the first time.

## Data sources and where each file goes

| # | Layer | Folder in data/raw/ | How it's obtained |
|---|---|---|---|
| 1 | SRTM DEM | `dem/` | Scriptable (OpenTopography API, free key) |
| 2 | Sentinel-2 (NDVI) | `sentinel2/` | Manual download (Copernicus account) |
| 3 | Sentinel-1 (deformation) | `sentinel1/` | Manual download + processing, see note below |
| 4 | CHIRPS rainfall | `chirps/` | Scriptable, no login |
| 5 | OpenStreetMap roads/streams | `osm/` | Scriptable, no login |
| 6 | SoilGrids | `soilgrids/` | Scriptable, no login |
| 7 | NASA COOLR landslide inventory | `coolr/` | Scriptable, no login |
| 8 | NBRO hazard zonation | `nbro/` | Manual download |

## A note on the InSAR deformation layer

Real SBAS-InSAR processing needs specialist software (ESA's SNAP, or the
ISCE/MintPy toolchain) and normally takes many hours of processing over a
stack of a dozen or more Sentinel-1 scenes. This is realistic for a research
group but heavy for a single assignment. Two honest options:

- **Full version**: download a Sentinel-1 stack and process it yourself in
  SNAP (free, GUI-based, has a guided SBAS workflow).
- **Scoped-down version**: treat the deformation layer as a documented
  limitation, note in the report that InSAR processing was out of scope
  given time constraints, and build the index on the other six layers, all
  of which are fully real. This is a legitimate methodological choice; the
  brief lists InSAR as one of several *optional* advanced techniques, not
  a requirement.

Decide this once the other six datasets are downloaded and working, no
need to decide now.
