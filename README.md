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
| AirGradient | Independent | 4 | 65 |
| SmartCitizenKit | Independent | 14 | 25 |
| Sensor.Community | Independent | 12 | 1,159 |

---

## Key Findings

### Four Integration Gaps

**Gap 1 - Spatial (93%)**  
93% of Italian ARPA stations have no independent sensor within 1km. In MCM the rate is 69% uncovered (only 5 of 16 ARPA stations have a nearby independent sensor).

**Gap 2 - Temporal (0 years)**  
No independent sensor in MCM has historical data before October 2024. All 37 Sensor.Community PM sensors in MCM are brand new with no archive. Maximum overlap period is 42 days (Dec 2024 - Jan 2025).

**Gap 3 - Pollutant Mismatch (2 of 7)**  
ARPA measures gas pollutants (NO2, O3, SO2) for regulatory compliance. Independent citizen sensors mainly measure particulate matter (PM2.5, PM10). Only PM2.5 and PM10 are common between both networks.

**Gap 4 - Data Quality (4.4x)**  
The one valid comparable pair (Milano Pascal ARPA vs SaliuA0A6 SCK) showed SCK reads 4.4x lower than ARPA for PM2.5 (R2=0.020) and 5.6x lower for PM10 (R2=0.004). Calibration is essential before integration.

---

## Notebook Structure

`Enhancing Air Quality Data Integration.ipynb` contains all analysis across 43 cells:

| Cells | Step | Description |
|---|---|---|
| 1-14 | Step 1 - Data Collection | Fetch and map all 5 networks inside MCM |
| 15-20 | Step 1 - Measurements | Download readings and build comparative table |
| 21-22 | Step 1 - Visualisation | Interactive Folium map + summary |
| 23-29 | Step 1 - Classification | Compare against official ARPA Lombardia CSV |
| 30-34 | Step 2 - Proximity Analysis | MCM proximity rate + Italy national analysis |
| 35 | Step 3 - Terminology | Rename overlap to proximity in all outputs |
| 36 | Step 3 - Typology | Station environment classification for all 5 pairs |
| 37 | Step 3 - Typology Map | Interactive HTML map with color coding |
| 38 | Step 3 - Reading Comparison | PM2.5 and PM10 comparison: ARPA vs SCK |
| 39-42 | Step 3 - Investigation | SC archive check, data availability for all pairs |

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

Install dependencies:
```bash
pip install requests pandas geopandas folium matplotlib scipy openpyxl shapely
```

---

## API Keys Required

Add these at the top of the notebook (Cell 2):

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

---

## Outputs

| File | Description |
|---|---|
| `comparative_table_final.csv` | All 5 networks: pollutants, dates, null rates |
| `station_comparison_independent.csv` | MCM proximity pairs - independent sensors only |
| `station_comparison_italy_independent.csv` | Italy proximity pairs |
| `AQ_Step1_Visual_Report.xlsx` | 7-sheet Excel report with charts |
| `typology_map.html` | Interactive map - station typology color coded |
| `sensor_comparison_plot.png` | PM2.5 and PM10 time series + scatter plots |

---

## Project Steps

### Step 1 - Network Discovery
Identified all air quality networks operating in MCM. Confirmed that OpenAQ EEA and AQICN re-publish official ARPA Lombardia data and are not independent. Final independent networks: AirGradient (4), SmartCitizenKit (14), Sensor.Community (12).

### Step 2 - Proximity Analysis
Calculated the proximity rate between ARPA official stations and independent sensors using a 1km threshold. MCM proximity rate: 31% (independent only). Italy proximity rate: 7% (93% of ARPA stations have no independent sensor nearby).

### Step 3 - Typology and Reading Comparison
Verified station typology (Traffic/Background) before comparing readings. Only 1 of 5 proximity pairs had both matching typology and usable data: Milano Pascal (Background Urban) vs SaliuA0A6 SCK (Background Urban, Citta Studi). Comparison period: Dec 2024 - Jan 2025 (42 days, 151 matched hourly pairs).

---

## Results Summary

| Metric | PM2.5 | PM10 |
|---|---|---|
| ARPA mean | 34.91 ug/m3 | 48.34 ug/m3 |
| SCK mean | 7.88 ug/m3 | 8.67 ug/m3 |
| R2 | 0.020 | 0.004 |
| Bias | -27.03 ug/m3 | -39.67 ug/m3 |
| SCK/ARPA ratio | 0.23 (4.4x lower) | 0.18 (5.6x lower) |

---

## Author

Praveenkumar Saminathan  
MSc GeoInformatics Engineering  
Politecnico di Milano  
