# 🚖 NYC Ride-Hailing Data Engineering & Enterprise Analytics (243.5M+ Records)

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![DuckDB](https://img.shields.io/badge/DuckDB-FFF000?style=for-the-badge&logo=duckdb&logoColor=black)
![Polars](https://img.shields.io/badge/Polars-CD7F32?style=for-the-badge&logo=polars&logoColor=white)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Star Schema](https://img.shields.io/badge/Star_Schema-Data_Warehouse-success?style=for-the-badge)

An end-to-end Enterprise Data Engineering & High-Performance Analytics pipeline processing **243.5 Million+ ride-hailing trips** across New York City (Uber & Lyft) for the entire year of 2025. The project features an OLAP Data Warehouse built on DuckDB, high-speed dataframe processing via Polars, advanced SQL window functions, and an executive 3-page Power BI dashboard with a custom Dark-Theme UI.

---

## 📌 Executive Summary

1. **Massive Scale Efficiency:** Engineered an OLAP analytical engine using **DuckDB + Polars**, allowing sub-second analytical querying across 243.5M records without memory bottlenecks.
2. **Market Dominance:** Uber holds a commanding **~74.5% gross revenue market share ($5.57B)**, outperforming Lyft ($1.97B) across all NYC boroughs and airport corridors.
3. **Wait Time & Tipping Dynamics:** Drivers arriving under **2 minutes** receive tips on **24.21% of trips** ($1.64 avg tip). Waiting over 5 minutes causes tipping probability to drop sharply below 17.3%.
4. **Airport Profitability Premium:** Airport Pickups yield **15.1% higher gross revenue** ($80.70 vs $70.11 on Uber) compared to dropoffs, making them the most lucrative routes for both platforms and drivers.
5. **The Speed vs. Congestion Paradox:** Early morning hours (04:00–05:00) yield the highest hourly driver pay (**$76.16/hr**) due to high speed and zero congestion, whereas afternoon peak hours yield the highest pay per mile (**$4.80/mi**) but lower hourly earnings due to severe traffic.

---

## 🔄 ETL Pipeline Architecture & Data Flow

```text
┌─────────────────────────┐
│ Raw TLC Parquet Files   │ (243.5M+ Raw Records)
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ Python ETL Engine       │ ──► Data Ingestion, Schema Enforcement,
│ (Polars & DuckDB)       │     Batch Processing & Type Casting
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ DuckDB OLAP Warehouse   │ ──► Dimensional Star Schema
│ (nyc_ride_hailing.db)   │     (fact_trips, dim_company, dim_location, dim_date)
└────────────┬────────────┘
             │
             ├─────────────────────────────────────────┐
             ▼                                         ▼
┌─────────────────────────┐               ┌─────────────────────────┐
│ Advanced SQL Analytics  │               │ Power BI Visual Layer   │
│ (Polars Analytics DF)   │               │ (DirectImport / OLAP)   │
└─────────────────────────┘               └─────────────────────────┘
```

### 🏗️ Data Architecture Lifecycle (Mermaid)

The project follows a high-performance Enterprise Data Engineering and BI lifecycle:

```mermaid
graph LR
A[Raw TLC Parquet Data] --> B(Python/Polars: Cleaning & Preprocessing)
B --> C{DuckDB: OLAP Engine}
C --> D[ETL: Fact Table]
C --> E[ETL: Dimension Tables]
D --> F((Power BI Data Model))
E --> F
F --> G[Advanced DAX & Business Logic]
G --> H[Executive Dashboard]
```

*(Note: If viewing outside GitHub, the pipeline follows: Raw TLC Parquet ➔ Python/Polars Vectorized Processing ➔ DuckDB OLAP Staging ➔ Star Schema Construction ➔ Power BI Import ➔ DAX Modeling ➔ Executive Visualization)*

### Pipeline Workflow

1. **Extraction:** Ingested multi-gigabyte raw TLC High-Volume For-Hire Vehicle (HVFHV) Parquet datasets.
2. **Transformation & Cleaning:** Leveraged **Polars** for fast vectorised operations, handling timestamp conversions, calculating wait times (`pickup - request`), speed (`miles / hour`), and derived financial margins.
3. **Loading & Warehousing:** Modeled and populated an optimized **Star Schema Data Warehouse** inside **DuckDB** with indexing and read-only concurrency configuration.
4. **Analytics Layer:** Executed high-performance SQL analytical queries for deep business insights and served aggregated models to **Power BI**.

---

## 🗄️ Data Warehouse Schema (Star Schema)

```text
                  ┌─────────────────┐
                  │   dim_company   │
                  ├─────────────────┤
                  │ PK  company_id  │
                  │     company_name│
                  └────────┬────────┘
                           │ 1
                           │
                           │ N
┌─────────────────┐       ┌┴────────────────────────────┐       ┌─────────────────┐
│   dim_location  │       │         fact_trips          │       │    dim_date     │
├─────────────────┤       ├─────────────────────────────┤       ├─────────────────┤
│ PK  location_id │1─────N│ FK  company_id              │N─────1│ PK  date_key    │
│     borough     │       │ FK  PULocationID            │       │     month_name  │
│     zone        │       │ FK  DOLocationID            │       │     day_type    │
│     service_zone│       │ FK  pickup_date_key         │       │     is_weekend  │
└─────────────────┘       │     request_datetime        │       └─────────────────┘
                          │     pickup_datetime         │
                          │     dropoff_datetime        │
                          │     trip_miles, trip_time   │
                          │     total_gross_revenue     │
                          │     driver_pay, tips        │
                          │     company_margin          │
                          │     cbd_congestion_fee      │
                          └─────────────────────────────┘
```

---

## 📊 Power BI Dashboard Architecture

The frontend is built as a **3-page Executive Power BI Report** optimized for dark mode (`#16191E`) with custom accent styling:

### Page 1: Overview (Financial Performance & Market Share)

Features KPI Blocks ($7.54B Revenue, 244M Trips), Market Share Clustered Bars, Net Revenue Share Donut, Monthly Revenue Trend, and Waterfall Margin Bridge.

![Page 1 - Overview](assets/page1_overview.png)

### Page 2: Operations (Spatial Demand & Fleet Dynamics)

Displays Top Active Zones, Service Zone Distribution, Geographic Demand, and a Dual-Axis `Demand & Wait Time` combo chart by time window.

![Page 2 - Operations](assets/page2_operations.png)

### Page 3: Economics (Pricing & Driver Economics)

Analyzes `Driver Pay Share %` by company, `Hourly Congestion Fee Revenue`, `Trip Distance vs. Fare` scatter plot with trendlines, and `Weekday vs. Weekend` earnings.

![Page 3 - Economics](assets/page3_economics.png)

---

## 🔍 Deep-Dive Python & DuckDB Analytics

The ad-hoc analytics module (`src/advanced_analytics.py`) executes high-performance analytical queries directly on the 243.5M+ record DuckDB OLAP engine using Polars dataframes.

---

### Query 1: Wait Time Impact on Passenger Tipping Behavior

```python
import duckdb
import polars as pl

con = duckdb.connect("nyc_ride_hailing_2025.duckdb", read_only=True)

query_wait_vs_tip = """
    SELECT 
        CASE 
            WHEN epoch(pickup_datetime - request_datetime) / 60.0 < 2 THEN '1. Under 2 min'
            WHEN epoch(pickup_datetime - request_datetime) / 60.0 BETWEEN 2 AND 5 THEN '2. 2-5 min'
            WHEN epoch(pickup_datetime - request_datetime) / 60.0 BETWEEN 5 AND 10 THEN '3. 5-10 min'
            ELSE '4. Over 10 min'
        END AS wait_time_bucket,
        COUNT(*) AS total_trips,
        ROUND(AVG(tips), 2) AS avg_tip_amount,
        ROUND(SUM(CASE WHEN tips > 0 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS tip_percentage
    FROM fact_trips
    GROUP BY wait_time_bucket
    ORDER BY wait_time_bucket;
"""

df_wait_tip = con.sql(query_wait_vs_tip).pl()
print(df_wait_tip)
con.close()
```

**Query Output:**

| wait_time_bucket | total_trips | avg_tip_amount ($) | tip_percentage (%) |
| --- | --- | --- | --- |
| 1. Under 2 min | 27,634,391 | $1.64 | 24.21% |
| 2. 2-5 min | 132,255,064 | $1.11 | 18.90% |
| 3. 5-10 min | 68,756,510 | $1.14 | 17.23% |
| 4. Over 10 min | 14,943,719 | $1.37 | 17.27% |

📌 **Business Takeaway:** Dispatch efficiency directly dictates gratuity behavior. Pickups executed under 2 minutes yield a peak **24.21%** tip probability ($1.64 average tip). Once wait times exceed 5 minutes, tipping rates drop and plateau at ~17.2%.

---

### Query 2: Airport Trip Profitability Analysis (JFK & LaGuardia)

```python
import duckdb
import polars as pl

con = duckdb.connect("nyc_ride_hailing_2025.duckdb", read_only=True)

query_airports = """
    SELECT 
        company_id,
        CASE 
            WHEN PULocationID IN (132, 138) THEN 'Airport Pickup'
            WHEN DOLocationID IN (132, 138) THEN 'Airport Dropoff'
            ELSE 'Standard Trip'
        END AS airport_trip_type,
        COUNT(*) AS total_trips,
        ROUND(AVG(total_gross_revenue), 2) AS avg_gross_revenue,
        ROUND(AVG(company_margin), 2) AS avg_company_margin,
        ROUND(AVG(driver_pay), 2) AS avg_driver_pay
    FROM fact_trips
    WHERE PULocationID IN (132, 138) OR DOLocationID IN (132, 138)
    GROUP BY company_id, airport_trip_type
    ORDER BY total_trips DESC;
"""

df_airports = con.sql(query_airports).pl()
print(df_airports)
con.close()
```

**Query Output:**

| company_id | airport_trip_type | total_trips | avg_gross_revenue ($) | avg_company_margin ($) | avg_driver_pay ($) |
| --- | --- | --- | --- | --- | --- |
| HV0003 (Uber) | Airport Dropoff | 7,511,410 | $70.11 | $19.01 | $40.66 |
| HV0003 (Uber) | Airport Pickup | 6,515,668 | $80.70 | $18.38 | $49.67 |
| HV0005 (Lyft) | Airport Dropoff | 2,744,378 | $62.49 | $13.60 | $38.67 |
| HV0005 (Lyft) | Airport Pickup | 2,476,365 | $71.92 | $11.81 | $48.09 |

📌 **Business Takeaway:** Airport Pickups generate **15.1% higher gross revenue** than dropoffs due to demand surges and airport fees. Uber commands superior unit economics, retaining $18.38–$19.01 net margin per airport trip, whereas Lyft captures $11.81–$13.60.

---

### Query 3: Driver Earnings & Efficiency Analysis (Speed vs. Congestion Paradox)

```python
import duckdb
import polars as pl

con = duckdb.connect("nyc_ride_hailing_2025.duckdb", read_only=True)

query_driver_efficiency = """
    SELECT 
        HOUR(pickup_datetime) AS pickup_hour,
        COUNT(*) AS total_trips,
        ROUND(AVG(driver_pay), 2) AS avg_driver_pay,
        ROUND(SUM(driver_pay) / (SUM(epoch(dropoff_datetime - pickup_datetime)) / 3600.0), 2) AS driver_pay_per_hour_worked,
        ROUND(SUM(driver_pay) / SUM(trip_miles), 2) AS driver_pay_per_mile
    FROM fact_trips
    GROUP BY pickup_hour
    ORDER BY driver_pay_per_hour_worked DESC;
"""

df_driver_eff = con.sql(query_driver_efficiency).pl()
print(df_driver_eff)
con.close()
```

**Query Output (Selected Windows):**

| pickup_hour | total_trips | avg_driver_pay ($) | driver_pay_per_hour_worked ($/hr) |
| --- | --- | --- | --- |
| 04:00 AM | 3,743,159 | $23.01 | $76.16 |
| 05:00 AM | 4,446,709 | $23.13 | $73.76 |
| 03:00 AM | 3,540,262 | $20.48 | $71.46 |
| 18:00 PM (Peak) | 14,281,332 | $20.29 | $60.42 |
| 17:00 PM (Peak) | 13,816,750 | $21.43 | $58.67 |

📌 **Business Takeaway:** Early morning shifts (04:00–05:00 AM) offer maximum time efficiency (**$76.16/hr**) as clear roads allow drivers to cover more distance per hour. Peak evening hours yield the highest rate per distance (**$4.80/mi**), but severe Manhattan congestion reduces net hourly earnings down to ~$58.67/hr.

---

### Query 4: Borough Market Share Dispersion

```python
import duckdb
import polars as pl

con = duckdb.connect("nyc_ride_hailing_2025.duckdb", read_only=True)

query_borough_share = """
    SELECT 
        loc.borough,
        fact.company_id,
        COUNT(*) AS total_trips,
        ROUND(SUM(fact.total_gross_revenue), 2) AS total_gross_revenue,
        ROUND(AVG(fact.trip_miles), 2) AS avg_trip_miles
    FROM fact_trips fact
    JOIN dim_location loc ON fact.PULocationID = loc.location_id
    GROUP BY loc.borough, fact.company_id
    ORDER BY loc.borough, total_trips DESC;
"""

df_borough_share = con.sql(query_borough_share).pl()
print(df_borough_share)
con.close()
```

**Query Output:**

| borough | company_id | total_trips | total_gross_revenue ($) | avg_trip_miles |
| --- | --- | --- | --- | --- |
| Bronx | HV0003 (Uber) | 23,680,225 | $491,180,000.00 | 4.40 mi |
| Bronx | HV0005 (Lyft) | 7,725,806 | $162,950,000.00 | 4.14 mi |
| Brooklyn | HV0003 (Uber) | 46,402,943 | $1,135,400,000.00 | 4.23 mi |
| Brooklyn | HV0005 (Lyft) | 18,909,401 | $459,540,000.00 | 4.01 mi |
| Queens | HV0003 (Uber) | 38,341,799 | $1,255,800,000.00 | 6.35 mi |
| Queens | HV0005 (Lyft) | 14,549,653 | $454,930,000.00 | 6.13 mi |
| Staten Island | HV0003 (Uber) | 2,927,924 | $71,005,000.00 | 5.43 mi |
| Staten Island | HV0005 (Lyft) | 881,957 | $20,140,000.00 | 4.50 mi |

📌 **Business Takeaway:** Uber maintains a consistent ~75% trip volume and revenue dominance across all 5 NYC boroughs. Queens represents the highest distance per trip (6.13–6.35 mi) due to airport logistics, making it the most revenue-dense borough outside Manhattan.

---

## 📁 Repository Structure

```text
nyc-ride-hailing-analytics/
│
├── assets/
│   ├── page1_overview.png
│   ├── page2_operations.png
│   └── page3_economics.png
│
├── src/
│   ├── etl_pipeline.py            # Polars & DuckDB data pipeline
│   ├── data_warehouse_setup.py    # Schema generation & table population
│   └── advanced_analytics.py      # Deep-dive DuckDB SQL analytical queries
│
├── power_bi/
│   ├── README.md                  # Report documentation
│   └── nyc_ride_hailing_2025.pbix # Complete Power BI Dashboard File
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## 💻 How to Run

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/nihatrza/nyc-ride-hailing-analytics.git
   cd nyc-ride-hailing-analytics
   ```

2. **Set Up Python Environment:**

   ```bash
   pip install duckdb polars
   ```

3. **Execute Analytics Script:**

   ```bash
   python src/advanced_analytics.py
   ```

4. **Open Power BI Dashboard:**
   Open `power_bi/nyc_ride_hailing_2025.pbix` in Power BI Desktop to inspect measures, visuals, and interactive filters.

---

## 👤 Author

**Nihat Rzaguluzada | Data Analyst & Engineer**

* **Education:** Information Technologies Undergraduate at Azerbaijan State University of Economics (UNEC)
* **Certifications:** DataCamp Associate Data Analyst | freeCodeCamp Data Analysis with Python
* **GitHub:** [github.com/nihatrza](https://github.com/nihatrza)
* **LinkedIn:** [linkedin.com/in/nihat-rzaguluzada](https://www.linkedin.com/in/nihat-rzaguluzade/)
