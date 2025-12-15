# Urban Heat Island - LATENT-SPACE - GenHack 4



### **LATENT-SPACE [16]** — GenHack 2025 : 


>- Akram M. Lrhorfi : ensimag
>- Ahmed Bennasser : ensimag
>- Brandon Siao Xian Ling : Imperial college London
>- Sara Bazouane : ensimag | Imperial college London

> [ Contact Team LATENT-SPACE [16] for usage permissions. ]


This hackaton project investigates **Urban Heat Islands (UHI)** by integrating multiple geospatial datasets to understand how vegetation density (NDVI) influences temperature patterns in urban environments. The analysis addresses a critical research question:

> **Does NDVI explain the temperature errors between satellite/reanalysis data and weather stations?**

### What are Urban Heat Islands?

Urban Heat Islands occur when densely built urban areas retain significantly more heat than surrounding rural landscapes, primarily due to:
- Low vegetation cover and reduced evaporative cooling
- Heat-absorbing materials (concrete, asphalt)
- Waste heat from buildings, traffic, and industry
- Urban geometry that traps longwave radiation

### Real-World Applications

- **Real Estate**: Heat-related property value adjustments
- **Energy**: Cooling demand forecasting and grid stress prediction
- **Public Health**: Heat mortality risk mapping for vulnerable populations
- **Urban Planning**: Green space optimization for climate adaptation
- **ESG Reporting**: Quantitative climate risk disclosure metrics

##  Datasets

This analysis combines complementary satellite and ground-based observations:

| Dataset | Purpose | Resolution | Coverage |
|---------|---------|------------|----------|
| **GADM** | Administrative boundaries | Vector polygons | Europe |
| **ERA5-Land** | Temperature & wind reanalysis | 0.1° (~9km) | Global, daily 2023 |
| **Sentinel-2** | High-resolution NDVI | 10-20m | Wien region, 2023 |
| **Sentinel-3 OLCI** | High-frequency NDVI timeseries | 300m | Daily, 2023 |
| **ECA&D Stations** | Ground-truth temperature | Point observations | Weather stations |

### Data Rationale

No single dataset provides both spatial detail AND temporal coverage needed for urban heat assessment:
- **ERA5**: Complete spatial coverage but coarse resolution → regional context
- **Sentinel-2**: Fine spatial detail → identify UHI hotspots
- **Sentinel-3**: High temporal frequency → track seasonal dynamics
- **Stations**: Point accuracy → validate and calibrate gridded products


##  Data Configuration

### Current Setup (Hard-Coded Paths)

The notebook uses absolute paths that must be adapted for your environment:

```python
# Located in Cell 2 (lines 101-120)
BASE_DIR = Path("/Users/[username]/hackathon_data")
SENTINEL2_NDVI_PATH = Path("/Users/[username]/.../sentinel2_ndvi")
SENTINEL3_NDVI_DIR = Path("/Users/[username]/.../sentinel3-olci-ndvi")
GADM_EUROPE_PATH = BASE_DIR / "gadm_410_europe.gpkg"
ERA5_LAND_DIR = BASE_DIR / "derived-era5-land-daily-statistics"
ECAD_TX_DIR = BASE_DIR / "ECA_blend_tx"
```

### To Adapt

1. **Obtain datasets** from the GenHack data repository
2. **Update paths** in the imports cell (`#VSC-202526ad`, lines 101-120)
3. **Verify access** to Google Drive shortcuts (if applicable)



##  Analysis Workflow

### 1. **GADM Administrative Boundaries** (Cells 5-10)
- Load Europe-wide GADM polygons
- Filter to study region (default: Wien, Austria)
- Dissolve polygons to unified region boundary

### 2. **ERA5-Land Climate Data** (Cells 19-23)
- Load temperature (`t2m`), wind (`u10`, `v10`) from NetCDF
- Use `xarray` with lazy loading (`chunks={'time': 10}`)
- Aggregate to daily statistics (mean, max, min)

### 3. **Sentinel-2 NDVI** (Cells 36-42)
- Load high-resolution GeoTIFF (10-20m)
- Crop to region boundary using `rasterio.mask()`
- Convert from uint8 (0-255) to float (-1.0 to 1.0)

### 4. **Sentinel-3 Timeseries** (Cells 48-87)
- Process daily NDVI rasters (300m resolution)
- Spatial masking to urban/rural zones
- Temporal aggregation for seasonal analysis

### 5. **Weather Station Data** (Cells 71-84)
- Parse ECA&D TX files (maximum temperature)
- Convert DMS coordinates to decimal degrees
- Spatial join stations to GADM regions

### 6. **UHI Quantification** (Cells 59-62)
- Create 30km buffer ring around city
- Compute city vs. ring NDVI/temperature differences
- Calculate UHI intensity metrics

### 7. **Regression Modeling** (Cells 62-94)
- **OLS Regression**: Linear model with statsmodels
- **Random Forest**: Non-linear ensemble method
- **PyTorch Neural Network**: Custom `UHI_NN_Deep` architecture

### 8. **Seasonal Analysis** (Cells 88-93)
- Summer (JJA) vs. winter (DJF) UHI comparisons
- Heatwave amplification effects
- Wind speed influence on UHI intensity



### All functions are defined **inline within notebook cells** :

| Function | Location | Purpose |
|----------|----------|---------|
| `load_era5_var()` | Line 551 | Load ERA5-Land NetCDF with xarray |
| `convert_ndvi()` | Line 1081 | Rescale Sentinel-2 NDVI from uint8 |
| `convert_s3()` | Line 1228 | Sentinel-3 NDVI conversion |
| `dms_to_decimal()` | Lines 1389, 1915, 3876 | DMS→decimal degrees (3 variants) |
| `build_ring()` | Line 5870 | Create buffer ring around region |
| `season_from_dates()` | Line 6459 | Map dates to seasons (DJF/MAM/JJA/SON) |
| `UHI_NN_Deep` | Line 8039 | PyTorch neural network class |



### Coordinate Systems

- **Analysis CRS**: `EPSG:4326` (WGS84 lat/lon) — used for all geospatial operations
- **Distance CRS**: `EPSG:3857` (Web Mercator) — used for buffer calculations

**Always reproject before operations**: `.to_crs(TARGET_CRS)`

### Memory Management

- **ERA5-Land**: Use `chunks={'time': 10}` in `load_era5_var()` to avoid loading full year
- **Sentinel-3**: Process one date at a time in loops
- **NDVI rasters**: Crop to region first, then perform calculations



### No formal unit tests. Validate via:

```python
# Sanity checks
print(gdf.shape, gdf.crs)

# Visual inspection
plt.figure(figsize=(12, 7))
gdf.plot()

# Statistical summaries
df.describe()
```

##  Machine Learning Models

### 1. OLS Regression (statsmodels)
```python
import statsmodels.api as sm
X = sm.add_constant(features)
model = sm.OLS(target, X).fit()
print(model.summary())
```



### 2. PyTorch Neural Network
```python
class UHI_NN_Deep(nn.Module):
    def __init__(self, input_dim, hidden_layer_sizes, activation_fn=nn.ReLU()):
        # Configurable architecture via hidden_layer_sizes list
        # Trained with MSE loss and Adam optimizer
```



##  Resources

- [ERA5-Land Documentation](https://confluence.ecmwf.int/display/CKB/ERA5-Land)
- [Sentinel-2 MSI](https://sentinels.copernicus.eu/web/sentinel/missions/sentinel-2)
- [Sentinel-3 OLCI](https://sentinels.copernaris.eu/web/sentinel/missions/sentinel-3)
- [ECA&D Climate Data](https://www.ecad.eu/)
- [GADM Database](https://gadm.org/)

##  Hackaton Timeline

- **Week 1**: Data exploration and preprocessing
- **Week 2**: NDVI and temperature data integration
- **Week 3**: UHI quantification methodology
- **Week 4**: Regression analysis and validation


