# Enhancing Air Quality Data Integration

**MSc GeoInformatics Engineering - GeoInformatics Project (GIP)**  
Politecnico di Milano | 5 Credits

---

## Project Overview

This project investigates how well independent citizen sensor networks cover the official ARPA air quality monitoring network in Milan (Metropolitan City of Milano - MCM) and Italy. The goal is to assess whether citizen sensor data can be integrated with official reference monitor data.

**Research Question:**  
How many official ARPA stations have an independent citizen sensor within 1km? Are the readings comparable? What are the gaps that prevent integration?

---

## Networks Studied

| Network | Type | Count in MCM | Count in Italy |
|---|---|---|---|
| OpenAQ (EEA) | Official ARPA (re-publisher) | 16 | 749 |
| AQICN | Official ARPA (re-publisher) | 3 | - |
| AirGradient | Independent | 4 | 67 |
| SmartCitizenKit | Independent | 14 | 25 |
| Sensor.Community | Independent | 12 | ~1,355 (growing) |

---

## Key Findings

### Four Integration Gaps

**Gap 1 - Spatial (93%)**  
93% of Italian ARPA stations have no independent sensor within 1km. In MCM the rate is 69% uncovered (only 5 of 16 ARPA stations have a nearby independent sensor).

**Gap 2 - Temporal (0 years)**  
No independent sensor in MCM has historical data before October 2024. All Sensor.Community PM sensors in MCM are brand new with no archive. Maximum comparison period is 42 days (Dec 2024 - Jan 2025).

**Gap 3 - Pollutant Mismatch (2 of 7)**  
ARPA measures gas pollutants (NO2, O3, SO2) for regulatory compliance. Independent citizen sensors mainly measure particulate matter (PM2.5, PM10). Only PM2.5 and PM10 are common between both networks.

**Gap 4 - Data Quality (3-5x)**  
The one valid comparable pair (Milano Pascal ARPA vs SaliuA0A6 SCK) showed SCK reads 3.2x lower than ARPA for PM2.5 and 4.7x lower for PM10. Calibration is essential before integration.

---

## Statistical Results - Reading Comparison

**Data source:** ARPA Lombardia direct API (sensor IDs: PM2.5=10283, PM10=10273)  
**Resolution:** Daily averages (gravimetric reference method - official regulatory measurement)  
**Period:** Dec 2024 - Jan 2025 (42 days) | **Matched pairs:** 24 days PM2.5, 25 days PM10

| Metric | PM2.5 ARPA | PM2.5 SCK | PM10 ARPA | PM10 SCK |
|---|---|---|---|---|
| Mean (ug/m3) | 36.08 | 11.14 | 56.00 | 11.94 |
| Median (ug/m3) | 35.50 | 7.08 | 50.00 | 7.71 |
| Std Dev (ug/m3) | 23.47 | 10.10 | 45.24 | 10.26 |
| Variance (ug/m3)^2 | 550.83 | 101.96 | 2046.56 | 105.18 |
| IQR (ug/m3) | 13.25 | 10.57 | 17.00 | 10.22 |
| R2 | 0.041 | - | 0.016 | - |
| Bias (ug/m3) | -24.94 | - | -44.06 | - |
| RMSE (ug/m3) | 34.34 | - | 63.04 | - |
| SCK/ARPA ratio | 0.31 (3.2x lower) | - | 0.21 (4.7x lower) | - |

---

## Notebook Structure

`Enhancing Air Quality Data Integration.ipynb` contains all analysis across 47 cells:

| Cells | Step | Description |
|---|---|---|
| 1-14 | Step 1 - Data Collection | Fetch and map all 5 networks inside MCM |
| 15-20 | Step 1 - Measurements | Download readings and build comparative table |
| 21-22 | Step 1 - Visualisation | Interactive Folium map + summary |
| 23-29 | Step 1 - Classification | Compare against official ARPA Lombardia CSV |
| 30-34 | Step 2 - Proximity Analysis | MCM proximity rate + Italy national analysis |
| 35 | Step 3 - Terminology | Rename overlap to proximity in all outputs |
| 36 | Step 3 - Typology | Station environment classification for all 5 pairs |
| 37 | Step 3 - Typology Map | Interactive HTML map - MCM proximity pairs color coded |
| 38 | Step 3 - ARPA Direct Fetch | Fetch PM2.5 and PM10 from ARPA Lombardia direct API and save as CSV |
| 39 | Step 3 - Reading Comparison | PM2.5 and PM10 comparison - ARPA vs SCK with full statistics and plots |
| 40-43 | Step 3 - Investigation | SC archive check, data availability for all 5 pairs |
| 44-46 | Step 3 - Italy Map | Full Italy interactive map - all ARPA + all independent sensors |
| 47 | - | Empty |

---

## ARPA Data Source

ARPA PM2.5 and PM10 data is fetched directly from the **ARPA Lombardia open data portal** - bypassing OpenAQ:

```
https://www.dati.lombardia.it/resource/nicp-bhqi.json
```

| Pollutant | Sensor ID | Name | Available From |
|---|---|---|---|
| PM2.5 | 10283 | Particelle sospese PM2.5 | 2007 |
| PM10 | 10273 | PM10 (SM2005) | 2007 |

Data type: **Daily averages** (gravimetric method - official regulatory measurement, gold standard).  
Data is saved locally to `arpa_pm25_saved.csv` and `arpa_pm10_saved.csv` to avoid repeated API calls.

---

## How the 3x3 Grid Query Works (Sensor.Community)

The Sensor.Community API has a radius limit per query. Italy is too large to cover in a single query. The country was divided into a 3x3 grid of 9 cells:

```
Italy bounding box: lat 36-47.5, lon 6-19

Grid 1,1  Grid 1,2  Grid 1,3   (South)
Grid 2,1  Grid 2,2  Grid 2,3   (Centre)
Grid 3,1  Grid 3,2  Grid 3,3   (North)
```

Each cell was queried separately using the SC area filter API. Results were merged and deduplicated by unique station ID to give the final count of approximately 1,355 unique PM sensors across Italy.

---

## Requirements

```
python >= 3.10
requests
pandas
geopandas
folium
matplotlib
scipy
openpyxl
shapely
```

Install:
```bash
pip install requests pandas geopandas folium matplotlib scipy openpyxl shapely
```

---

## API Keys Required

Add at the top of the notebook (Cell 2):

```python
OPENAQ_KEY       = "your_openaq_api_key"
AQICN_DATA_TOKEN = "your_aqicn_token"
```

- OpenAQ API key: https://explore.openaq.org
- AQICN token: https://aqicn.org/data-platform/token

---

## Data Files Required

Place in the same directory as the notebook:

- `MCM.gpkg` - Metropolitan City of Milano boundary (GeoPackage)
- `Elenco-stazioni-rete-rilevamento-qualita-aria.csv` - Official ARPA Lombardia station list

Auto-generated by the notebook (Cell 38):

- `arpa_pm25_saved.csv` - ARPA PM2.5 daily values Dec 2024 - Jan 2025
- `arpa_pm10_saved.csv` - ARPA PM10 daily values Dec 2024 - Jan 2025

---

## Outputs

| File | Description |
|---|---|
| `comparative_table_final.csv` | All 5 networks: pollutants, dates, null rates |
| `station_comparison_independent.csv` | MCM proximity pairs - independent sensors only |
| `station_comparison_italy_independent.csv` | Italy proximity pairs - all 749 ARPA stations |
| `AQ_Step1_Visual_Report.xlsx` | 7-sheet Excel report with charts |
| `typology_map.html` | Interactive map - MCM station typology color coded |
| `sensor_comparison_plot.png` | PM2.5 and PM10 time series and scatter plots with full statistics |
| `italy_map.html` | Interactive map - all Italy ARPA and independent sensors |

---

## Project Steps

### Step 1 - Network Discovery
Identified all air quality networks operating in MCM. Confirmed that OpenAQ EEA and AQICN re-publish official ARPA Lombardia data and are not independent. Final independent networks: AirGradient (4), SmartCitizenKit (14), Sensor.Community (12).

### Step 2 - Proximity Analysis
Calculated the proximity rate between ARPA official stations and independent sensors using a 1km threshold. MCM proximity rate: 31% (independent only). Italy proximity rate: approximately 7% (93% of ARPA stations have no independent sensor nearby). Italy-wide data fetched using 3x3 grid method for Sensor.Community.

### Step 3 - Typology and Reading Comparison
Verified station typology (Traffic/Background) before comparing readings. Only 1 of 5 proximity pairs had both matching typology and usable data: Milano Pascal (Background Urban) vs SaliuA0A6 SCK (Background Urban, Citta Studi). ARPA data fetched directly from ARPA Lombardia open data portal using sensor IDs 10283 (PM2.5) and 10273 (PM10). Data resolution is daily averages (gravimetric reference). Comparison period: Dec 2024 - Jan 2025 (42 days, 24-25 matched daily pairs). Full statistical analysis: mean, median, standard deviation, variance, IQR, R2, bias, RMSE and ratio.

---

## Author

Praveenkumar Saminathan  
MSc GeoInformatics Engineering  
Politecnico di Milano  
