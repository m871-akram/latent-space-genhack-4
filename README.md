# Urban Heat Island — LATENT-SPACE [16] — GenHack 2025

### **LATENT-SPACE [16]** — GenHack 2025

>- Akram M. Lrhorfi : ensimag
>- Ahmed Bennasser : ensimag
>- Brandon Siao Xian Ling : Imperial College London
>- Sara Bazouane : ensimag | Imperial College London


---

ERA5-Land reanalysis temperatures carry a **systematic bias** against ground stations that
is largest in cities, complex terrain and continental interiors. This project **quantifies
that bias, explains it, and corrects it** by combining reanalysis, satellite vegetation,
and ground-truth station data.

**Headline results (single notebook, fully reproducible):**

- ERA5 shows a **cold bias of ≈ −1.24 °C** over the Wien region (largest in spring/summer),
  measured against real ECA&D stations using **polygon-masked** city/ring means (not bounding boxes).
- A simple **linear correction** (NDVI + wind + season), validated **leave-one-station-out**,
  removes the systematic bias (→ ≈ 0) and cuts error **RMSE 1.96 → 1.46 °C** (~26 %).
- Scaling to a **multi-year (2020–2023), multi-region** dataset (Wien, SE England,
  Emilia-Romagna; **n ≈ 5,500 station×season×year from 359 stations**) makes **NDVI a
  statistically significant** predictor (p < 1e-4) and adds **distance-to-coast** as the
  second-strongest driver. Out-of-sample (leave-one-station-out) **RMSE ≈ 1.47 °C**.
- The result is packaged as a **deployable correction**:
  `T_adjusted = T_ERA5 − biaŝ(NDVI, season, distance-to-coast)`.

**Driver ranking (standardized):** season ≫ continentality (distance-to-coast) > vegetation (NDVI).
The bias is *first* a seasonal phenomenon, *then* a continental one, with vegetation a real
but secondary modulator.

---

> **Does NDVI explain the temperature error between ERA5 reanalysis and weather stations?**

Hypothesis (from the challenge brief): ERA5 is *more* accurate over vegetation (high biomass)
and *less* accurate over dense, low-vegetation urban zones, because of the Urban Heat Island
(UHI) effect and the difficulty of physically modelling complex urban surfaces at ~9 km.

### Real-world applications
- **Real estate / ESG** — heat-risk and climate-disclosure metrics free of ERA5's urban cold bias.
- **Energy** — cooling-demand and grid-stress forecasts keyed to realistic urban peak temperatures.
- **Public health** — heat-mortality risk mapping for low-vegetation, inland neighbourhoods.
- **Urban planning** — green-space siting for climate adaptation.

---

## Data

| Dataset | Purpose | Resolution / Coverage |
|---|---|---|
| **GADM** | Administrative boundaries (study regions) | Vector polygons, Europe |
| **ERA5-Land daily** | Temperature max, wind, precipitation | ~0.1° (~9 km), daily, **2020–2025** |
| **Sentinel-2 NDVI** | Vegetation index (urban/rural classification, station NDVI) | seasonal composites, **2020–2023** |
| **Sentinel-3 OLCI NDVI** | High-frequency NDVI dynamics | ~300 m, daily |
| **ECA&D TX** | Ground-truth daily max temperature | Station points (the bias reference) |
| **Natural Earth — Ocean (50 m)** | Distance-to-coast feature | cached at `data/ne_ocean_50m.gpkg` |

---

## Setup

```bash
pip install -r requirements.txt
```

Data paths are resolved from **environment variables** (no hard-coded paths in the logic);
sensible Google-Drive defaults are baked in and the config cell **fails fast** with a clear
message if anything is missing:

| Variable | Points to |
|---|---|
| `GENHACK_GDRIVE_ROOT` | Drive `data_genhack` root |
| `GENHACK_DATA_DIR` | ERA5 / GADM / Sentinel-2 (`…/main`) |
| `GENHACK_ECAD_DIR` | Locally-extracted `ECA_blend_tx` |
| `GENHACK_S2_FILE` | Sentinel-2 composite for classification (default: 2023 summer) |
| `GENHACK_S3_DIR` | Sentinel-3 OLCI NDVI |


```bash
jupyter nbconvert --to notebook --execute --inplace "week{4}_team{16}.ipynb"
```

---

## Notebook Structure


- **Week 1 — Data exploration & integration.** GADM regions, ERA5-Land fields, Sentinel-2/3
  NDVI, ECA&D stations.
- **Week 2 — UHI visualization & communication.** Vegetation dynamics, side-by-side S2/S3
  NDVI, station temperature series.
- **Week 3 — Metrics & quantitative analysis.** Polygon-masked UHI intensity, seasonal/occurrence/
  heatwave/lag metrics, ERA5–station discrepancy, the NDVI↔bias ("Graal") test, weather
  conditioning (wind & precipitation), a **three-climate comparison** (continental / maritime /
  Po-valley), and altitude-vs-bias.
- **Week 4 — Modelling & correction.** Model 1 (linear, standardized coefficients), Model 2
  (NDVI-only), the **multi-year multi-region driver model**, Model 3 (exploratory deep NN), an
  **operational correction & deployment** section, and an honest Limitations section.

---

## Diagnosis (key findings)

- **Cold bias, seasonal shape.** ERA5 − station ≈ −1.24 °C over Wien, most negative in
  spring/summer; correctly recovered with point-in-polygon city/ring masking.
- **UHI is detectable but small at 9 km.** A 90th-percentile UHI threshold of 0.539 °C is
  exceeded on ~37 days in 2023; wind ventilates the effect (≈ −0.15 °C per m/s).
- **Three climates differ.** Continental Wien, maritime SE England and the Po-valley
  Emilia-Romagna show distinct NDVI seasonality, UHI distributions and bias structure.
- **Altitude matters.** ERA5 bias varies systematically with station elevation.

---

## Limitations

- **ERA5 resolution (~9 km).** Wien spans only ~4 ERA5 cells; reported ΔT are regional
  contrasts, not street-level UHI.
- **NDVI signal is scale-dependent.** Significant once enough stations/years are pooled
  (p < 1e-4), but with a sign that depends on within-city vs across-region scale.
- **Bias is mostly seasonal/continental.** The residual sub-grid urban signal is small and not
  fully resolvable at 9 km — which is exactly why ground stations remain essential.
- **NN comparability.** The deep NN's hold-out differs from the linear models', so absolute
  error numbers are not directly comparable across models.

---

## Resources
- [ERA5-Land](https://confluence.ecmwf.int/display/CKB/ERA5-Land) ·
  [Sentinel-2](https://sentinels.copernicus.eu/web/sentinel/missions/sentinel-2) ·
  [Sentinel-3 OLCI](https://sentinels.copernicus.eu/web/sentinel/missions/sentinel-3) ·
  [ECA&D](https://www.ecad.eu/) · [GADM](https://gadm.org/) ·
  [Natural Earth](https://www.naturalearthdata.com/)
