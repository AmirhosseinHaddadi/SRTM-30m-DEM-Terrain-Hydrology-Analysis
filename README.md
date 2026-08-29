# SRTM-30m-DEM-Terrain-Hydrology-Analysis
A Python/Jupyter workflow for downloading SRTM 1 arc-second DEM tiles, clipping them to a user-defined area of interest (AOI), reprojecting the DEM to a local 30 m UTM grid, and generating a comprehensive set of terrain and hydrologic products.

Features

Downloads SRTM .hgt.gz tiles directly from the AWS Skadi archive.

Supports resumable downloads, retry logic, local caching, and HGT file validation.

Accepts AOIs from Shapefile, GeoPackage, GeoJSON, ZIP, or an interactive ipyleaflet map.

Mosaics and clips SRTM tiles to the exact AOI geometry.

Automatically selects the local UTM zone and resamples the DEM to 30 m.

Derives slope, aspect, hillshade, curvature, TPI, TRI, and roughness.

Fills depressions and resolves flats for hydrologic routing.

Computes D8 flow direction, flow accumulation, drainage area, and TWI.

Extracts a drainage network using a configurable contributing-area threshold.

Exports stream vectors in both projected UTM coordinates and WGS84.

Workflow

AOI
 │
 ├─► SRTM tile selection
 │
 ├─► Download / cache HGT tiles
 │
 ├─► Mosaic and exact AOI clip
 │
 ├─► Automatic local UTM reprojection at 30 m
 │
 ├─► Terrain derivatives
 │    ├─ Slope / slope percent
 │    ├─ Aspect / hillshade
 │    ├─ Curvature
 │    ├─ TPI / TRI / roughness
 │
 └─► Hydrologic analysis
      ├─ Pit and depression filling
      ├─ Flat resolution
      ├─ D8 flow direction
      ├─ Flow accumulation
      ├─ Drainage area
      ├─ TWI
      └─ Stream raster + vector network

Requirements

Python 3 with the following packages:

pip install "rasterio>=1.4" geopandas shapely pyproj requests tqdm \
    ipyleaflet ipywidgets matplotlib scipy pysheds

The notebook includes an installation cell for convenience.

Quick Start

Clone or download this repository.

Open SRTM30_DEM_Analysis.ipynb in Jupyter Notebook, JupyterLab, VS Code, or Google Colab.

Choose an AOI input mode in the configuration cell:

MODE = "shapefile" for a local vector boundary.

MODE = "map" for interactive selection.

Set OUTPUT_DIR and, optionally, SHAPEFILE_PATH.

Adjust STREAM_THRESHOLD_KM2 or other processing parameters if needed.

Run the notebook from top to bottom.

Configuration

Parameter

Description

Default

MODE

AOI input mode

"shapefile"

SHAPEFILE_PATH

Vector AOI path; blank triggers an input prompt

""

OUTPUT_DIR

Root output folder

./outputs

OUTPUT_NAME

Name of the clipped geographic DEM

SRTM30_DEM.tif

TARGET_RESOLUTION

Projected DEM cell size

30 m

STREAM_THRESHOLD_KM2

Minimum drainage area for stream extraction

2.0 km²

TPI_WINDOW

Moving-window size used for TPI

9 cells

MAX_TILES

Maximum SRTM tiles allowed per run

500

KEEP_HGT

Keep extracted HGT files after processing

True

KEEP_MOSAIC

Keep the temporary bounding-box mosaic

False

Example Run

The figures below are the actual outputs embedded in the supplied notebook. In that run:

AOI bounds: 51.089010, 35.568217, 51.606078, 35.828525

SRTM tile: N35E051

Native SRTM CRS: EPSG:4326

Elevation range: 1029–2033 m

Mean elevation: 1296.19 m

Automatically selected analysis CRS: EPSG:32639 (UTM Zone 39N)

Stream threshold: 2.0 km² (2223 cells)

Extracted vector stream segments: 164

Results Gallery

DEM Preparation

Clipped SRTM DEM

Projected 30 m UTM DEM

<img src="assets/clipped_srtm_dem.png" alt="Clipped SRTM DEM" width="100%">

<img src="assets/projected_dem_30m_utm.png" alt="Projected 30 m UTM DEM" width="100%">

Filled DEM

Hydrologically Conditioned DEM

<img src="assets/filled_dem.png" alt="Filled DEM" width="100%">

<img src="assets/conditioned_dem.png" alt="Hydrologically conditioned DEM" width="100%">

Depression Analysis

Sink Depth

Sink Mask

<img src="assets/sink_depth.png" alt="Sink depth" width="100%">

<img src="assets/sink_mask.png" alt="Sink mask" width="100%">

Terrain Derivatives

Slope (Degrees)

Slope (Percent)

<img src="assets/slope_degrees.png" alt="Slope in degrees" width="100%">

<img src="assets/slope_percent.png" alt="Slope in percent" width="100%">

Aspect

Hillshade

<img src="assets/aspect.png" alt="Aspect" width="100%">

<img src="assets/hillshade.png" alt="Hillshade" width="100%">

Laplacian Curvature

Topographic Position Index

<img src="assets/laplacian_curvature.png" alt="Laplacian curvature" width="100%">

<img src="assets/topographic_position_index.png" alt="Topographic Position Index" width="100%">

Terrain Ruggedness Index

Roughness

<img src="assets/terrain_ruggedness_index.png" alt="Terrain Ruggedness Index" width="100%">

<img src="assets/roughness.png" alt="Terrain roughness" width="100%">

Hydrologic Products

D8 Flow Direction

D8 Flow Accumulation

<img src="assets/d8_flow_direction.png" alt="D8 flow direction" width="100%">

<img src="assets/d8_flow_accumulation.png" alt="D8 flow accumulation" width="100%">

Drainage Area

Topographic Wetness Index

<img src="assets/drainage_area.png" alt="Drainage area" width="100%">

<img src="assets/topographic_wetness_index.png" alt="Topographic Wetness Index" width="100%">

Stream Network

Stream Raster

Stream Network over DEM

<img src="assets/stream_raster.png" alt="Stream raster" width="100%">

<img src="assets/stream_network_dem_overlay.png" alt="Stream network over DEM" width="100%">

<p align="center">
  <img src="assets/stream_network.png" alt="Final stream network" width="75%">
</p>

Output Structure

A typical run creates the following structure:

outputs/
├── SRTM30_DEM.tif
├── tiles/
│   └── ... cached SRTM HGT files ...
└── dem_products/
    ├── dem_30m_utm.tif
    ├── filled_dem.tif
    ├── conditioned_dem.tif
    ├── sink_depth.tif
    ├── sink_mask.tif
    ├── slope_degrees.tif
    ├── slope_percent.tif
    ├── aspect.tif
    ├── hillshade.tif
    ├── laplacian_curvature.tif
    ├── tpi.tif
    ├── tri.tif
    ├── roughness.tif
    ├── flow_direction_d8.tif
    ├── flow_accumulation_cells.tif
    ├── drainage_area_km2.tif
    ├── twi.tif
    ├── streams_d8.tif
    ├── streams_d8.shp
    └── streams_d8_wgs84.shp

ESRI Shapefiles also include their companion .dbf, .shx, .prj, and .cpg files.

D8 Direction Encoding

Direction

Code

Direction

Code

E

1

W

16

SE

2

NW

32

S

4

N

64

SW

8

NE

128

Method Notes

The analysis DEM is projected before terrain/hydrology calculations so distances and areas are expressed in metric units.

Flow routing is based on a hydrologically conditioned surface generated by filling pits and depressions and resolving flats.

Drainage area is calculated from D8 accumulation and raster cell area.

The stream network is threshold-based; reducing STREAM_THRESHOLD_KM2 produces a denser network, while increasing it retains only larger drainage paths.

TWI uses numerical lower bounds for slope and contributing area to avoid undefined values.

Preview plots may use percentile clipping or logarithmic transforms for visualization only; the saved rasters retain the computed data values.

Repository Files

.
├── README.md
├── SRTM30_DEM_Analysis.ipynb
├── requirements.txt
├── .gitignore
└── assets/
    └── ... output preview images ...

Notes

SRTM coverage is validated against the standard latitude range used by this workflow. Large AOIs may require many tiles and substantial disk space, so MAX_TILES is included as a safeguard.

The example images in this repository come from one completed notebook run and are included to make the processing results visible directly on GitHub without requiring the notebook to be executed first.
