# ls-lrsi-aranayake

Location Specific Landslide Risk Scoring Index (LS-LRSI) for Aranayake /
Devanagala, Kegalle District, Sri Lanka, centred on the confirmed
coordinate of the 17 May 2016 landslide (7.218418 N, 80.339762 E).

## What this is

A landslide risk score, on a 30 m grid (221 x 221 cells, UTM Zone 44N),
built from six real indicators: slope, vegetation cover (NDVI), rainfall,
distance to road, distance to stream, and soil clay content. Built for
DAS7003 Geospatial Analysis, PRAC1.

## Project structure

    project/
    ├── README.md
    ├── data/
    │   ├── raw/            # downloaded DEM, Sentinel-2, CHIRPS, OSM
    │   └── processed/      # reprojected, resampled to the shared 30 m grid
    ├── notebooks/
    │   ├── 01_data_acquisition.ipynb   # loads and checks every raw file
    │   ├── 02_preprocessing.ipynb      # reprojection, resampling, feature engineering
    │   ├── 03_eda.ipynb                # summary stats, histograms, spatial maps, correlation
    │   ├── 04_index_construction.ipynb # normalisation, weighting, scoring, sensitivity analysis
    │   └── 05_validation.ipynb         # independent check against the confirmed 2016 landslide site
    └── outputs/
        ├── figures/                    # all report figures
        ├── ls_lrsi_risk_map.tif        # classified risk (1=Low to 4=Very High)
        ├── ls_lrsi_risk_score.tif      # continuous 0-1 score
        └── ls_lrsi_full_results.csv    # every indicator + score + class, per grid cell

## How to run

1. Install Python 3.10 or newer.
2. Install the libraries:

       pip install numpy pandas matplotlib scipy rasterio geopandas requests jupyter matplotlib-scalebar

3. Download the raw data (see below) and place each file in `data/raw/`
   using the folder names listed in the table.
4. Run the notebooks in order, 01 through 05. Each one reads from either
   `data/raw/` or `data/processed/` and writes its results forward to the
   next notebook, so they need to run in sequence the first time.

## Data sources and where each file goes

| # | Layer | Folder in data/raw/ | How it's obtained |
|---|---|---|---|
| 1 | SRTM DEM | `dem/` | Scriptable (OpenTopography API, free key) |
| 2 | Sentinel-2 (NDVI) | `sentinel/` | Manual download (Copernicus Browser account) |
| 3 | CHIRPS rainfall | `chirps/` | Scriptable, no login |
| 4 | OpenStreetMap roads/streams | `osm/` | Scriptable, no login |
| 5 | SoilGrids (soil clay) | none, queried live | REST API, 25 points sampled and interpolated (`02_preprocessing.ipynb`) |
| 6 | NASA COOLR landslide inventory | none, queried live | Live API, with an automatic fallback to a single confirmed NBRO location record if the service is unavailable (`01_data_acquisition.ipynb`) |

## On the InSAR deformation layer

Ground deformation (InSAR) was part of the original index design but is
**not included** in this implementation, this was a deliberate, documented
scope decision, not an oversight. Real SBAS-InSAR processing needs
specialist software (ESA's SNAP, or the ISCE/MintPy toolchain) and a
substantial multi-step workflow (co-registration, interferogram
generation, phase unwrapping, atmospheric correction, time-series
inversion), judged out of scope for this assignment's timeframe. This is
discussed as a limitation in the final report and flagged as the top
recommendation for future work.

## A note on reproducibility

The soil clay layer depends on a live, rate-limited external API
(SoilGrids) and can return slightly different results between separate
runs (typically 21-25 of 25 points succeed). The figures and numbers
reported in the final report come from one specific, internally
consistent run; re-running the pipeline from scratch may produce small
numerical differences for this reason.