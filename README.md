# Enhancing Air Quality Data Integration

**MSc GeoInformatics Engineering | Politecnico di Milano | May 2026**

GitHub: https://github.com/Orange3456/Enhancing-Air-Quality-Data-Integration

---

## Project Overview

This project investigates whether independent citizen sensor networks can meaningfully complement the official ARPA Lombardia air quality monitoring network in the Metropolitan City of Milano (MCM) and across Italy. The project covers network discovery, proximity analysis, reading comparison and four integration gaps that currently prevent direct data integration.

---

## Key Findings

| Finding | Value |
|---|---|
| Networks studied | 5 |
| Independent sensors in MCM | 34 |
| Official ARPA stations in Italy | 749 |
| Independent sensors in Italy | 1,211 |
| Italy proximity rate | 6% (46 of 749 matched) |
| Italy spatial gap | 94% (703 ARPA stations uncovered) |
| MCM proximity rate (independent only) | 31% (5 of 16 matched) |
| Valid reading comparison pairs | 1 of 5 |
| PM2.5 R2 (ARPA vs SCK) | 0.041 |
| SCK underestimation PM2.5 | 3.2x lower (ratio 0.31) |

---

## Project Structure

```
AQ_Station_DataCollection.ipynb   Main project notebook (44 cells)
comparative_table_final.csv        All 5 networks - data availability and null rates
station_comparison_independent.csv MCM ARPA vs independent sensors
station_comparison_italy_independent.csv Italy ARPA vs independent sensors
AQ_Step1_Visual_Report.xlsx        Excel report with 7 sheets and charts
italy_map.html                     Interactive Italy map - ARPA vs independent sensors
typology_map.html                  Interactive MCM typology map
aq_stations.png                    MCM all networks static map
stations_comp.png                  MCM classification result map
AQ_Italy_Stations.png              Italy full map screenshot
sensor_comparison_plot.png         PM2.5 and PM10 comparison plots
```

---

## Step 1 - Network Discovery (Cells 1-29)

Five networks were discovered and investigated inside MCM:

| Network | Count MCM | Classification | Data From | Null % |
|---|---|---|---|---|
| OpenAQ (EEA) | 16 | Official ARPA re-publisher | May 2020 | 38-71% |
| AQICN | 3 | Official ARPA re-publisher | May 2020 | 18.2% |
| AirGradient | 4 | Independent citizen sensor | Oct 2024 | 85.5% |
| SmartCitizenKit | 14 | Independent citizen sensor | Dec 2024 | 98.3% |
| Sensor.Community | 16 | Independent citizen sensor | Jan 2017 | 0% |

**Key finding:** OpenAQ (EEA) and AQICN both re-publish official ARPA Lombardia data. They are not independent. The provider field in the OpenAQ API response confirms EEA as the source. AQICN response text contains the phrase "measured by Agenzia Regionale per la Protezione dell Ambiente della Lombardia".

**True independent sensors in MCM: 34** (4 AG + 14 SCK + 16 SC)

**Initial proximity rate (all networks): 81%** - misleading because OpenAQ EEA was matching against the same ARPA stations it re-publishes.

---

## Step 2 - Proximity Analysis (Cells 30-35)

**MCM (independent sensors only):**
- 5 of 16 ARPA stations (31%) have an independent sensor within 1km
- 11 of 16 ARPA stations (69%) have no independent sensor nearby

**Italy (national scale):**
- 749 official ARPA stations across Italy
- 1,211 independent sensors: 1,119 SC + 67 AG + 25 SCK
- 46 of 749 ARPA stations (6%) have an independent sensor within 1km
- 703 of 749 ARPA stations (94%) have no independent sensor within 1km

**Sensor.Community Italy method:** Italy is too large for a single SC API query (radius limit). Italy was divided into a 3x3 grid of 9 cells. Each cell was queried separately. Results were merged, deduplicated by station ID, and filtered strictly inside the Italy boundary polygon (ITA_adm0.shp). The SC API returns live active sensors - the count reflects sensors online at the time of the query (1,119 in this run).

---

## Step 3 - Reading Comparison (Cells 36-40)

**Typology check first (Vasil correction):** Before comparing readings, station typology was verified. Comparing a Traffic Urban ARPA station with a Background Urban citizen sensor is invalid even if they are within 1km - they measure different air.

| ARPA Station | Type | Independent Sensor | Valid? | Reason |
|---|---|---|---|---|
| Milano Verziere | Traffic Urban | AirGradient Milano | NO | No PM parameters |
| Milano Pascal C.S. | Background Urban | SaliuA0A6 SCK | YES | Both BU - valid |
| Milano v.Senato | Traffic Urban | CAL 001 SCK | NO | Device never configured |
| Sesto S.Giovanni | Traffic Urban | SC sensor | NO | Typology mismatch |
| Cinisello Balsamo | Traffic Urban | SC sensor | NO | No archive data |

**Only 1 valid pair: Milano Pascal (ARPA) vs SaliuA0A6 (SCK device 18286)**

**Data source:** ARPA Lombardia direct API (dati.lombardia.it, Socrata). Sensor IDs 10283 (PM2.5) and 10273 (PM10). OpenAQ was replaced as the data source because it returned HTTP 500 errors after the May 5 meeting. The ARPA direct API provides official gravimetric measurements - the gold standard method.

**Statistical results (24-25 matched days, Dec 2024 - Jan 2025):**

| Metric | ARPA PM2.5 | SCK PM2.5 | ARPA PM10 | SCK PM10 |
|---|---|---|---|---|
| Mean (ug/m3) | 36.08 | 11.14 | 56.00 | 11.94 |
| Median (ug/m3) | 35.50 | 7.08 | 50.00 | 7.71 |
| Std Dev (ug/m3) | 23.47 | 10.10 | 45.24 | 10.26 |
| Variance | 550.83 | 101.96 | 2046.56 | 105.18 |
| IQR (ug/m3) | 13.25 | 10.57 | 17.00 | 10.22 |
| R2 | 0.041 | - | 0.016 | - |
| Bias SCK-ARPA | -24.94 | - | -44.06 | - |
| RMSE | 34.34 | - | 63.04 | - |
| SCK/ARPA ratio | 0.31 (3.2x lower) | - | 0.21 (4.7x lower) | - |

**R2 = 0.041 means near-zero correlation.** Only 4% of SCK variation is explained by ARPA values. Despite matching Background Urban typology, the sensors do not agree.

---

## The Four Integration Gaps

| Gap | Value | Meaning |
|---|---|---|
| Spatial | 94% | 703 of 749 Italian ARPA stations have no independent sensor within 1km |
| Temporal | 0 years | No independent sensor in MCM has data before October 2024 |
| Pollutant | 2 of 7 | Only PM2.5 and PM10 are common. ARPA measures gases (NO2, O3, SO2) - citizen sensors do not |
| Quality | 3-5x | SCK reads 3.2x lower than ARPA. R2=0.041. Calibration essential before integration |

---

## Data Sources

| Source | URL | Authentication |
|---|---|---|
| OpenAQ v3 | api.openaq.org/v3 | API Key in header |
| AQICN | aqicn.org/data-platform | Token in URL |
| SmartCitizenKit | api.smartcitizen.me/v0 | None required |
| Sensor.Community | data.sensor.community/airrohr/v1/filter | None required |
| ARPA Lombardia | dati.lombardia.it (Socrata) | None required |

---

## Technical Notes

- CRS: WGS84 (EPSG:4326) for all coordinates. UTM 32N (EPSG:32632) used for distance calculations.
- Proximity threshold: 0.01 degrees (~1km at Milan latitude)
- Italy boundary: ITA_adm0.shp shapefile for polygon filtering
- SC API returns live active sensors only - count varies by query time
- ARPA gravimetric data: daily averages collected on filter paper and weighed in lab

---

## Author

Praveenkumar Saminathan
MSc GeoInformatics Engineering, 
Politecnico di Milano
