# PickBy-Transport-Logistics-Fleet-Analytics
An end-to-end Power BI analytics project analyzing revenue, service reliability, fleet efficiency, fuel costs, maintenance, driver performance, and safety for a fictional transportation and logistic company. 
 
# Project Presentation

PickBy Transport & Logistics is a fictional trucking and logistics company operating across a fleet of trucks, trailers, drivers, routes, customers, and delivery facilities.

The company generates operational data across multiple areas of the business, including loads, trips, fuel purchases, maintenance, delivery events, driver performance, truck utilization, and safety incidents.

The objective of this project was to transform multiple raw CSV datasets into a structured analytical model in Power BI and use the resulting model to answer important business questions around:

* Revenue performance
* Service reliability
* Fleet and fuel efficiency
* Maintenance costs
* Driver performance
* Safety performance

The project follows an end-to-end data analytics workflow:

Raw CSV Data → Power Query → Data Cleaning → Data Modeling → DAX Measures → Power BI Dashboards → Business 
## 📊 Project Overview

Project Type

Data Analytics / Business Intelligence / Power BI Capstone

Industry

Transportation & Logistics

Dataset

14 related CSV tables

Analysis Period

2022–2024

Records

Approximately 85,410 loads

Primary Tool

Microsoft Power BI

Supporting Tools

* Power Query
* DAX
* Excel
* CSV
* PowerPoint

# 🎯 Business Objectives

The project was designed around four major business areas.

1. Business Performance

Understand overall revenue performance and operational volume.

Key questions:

* How much revenue is the business generating?
* How does revenue change over time?
* Which customers and routes contribute most to revenue?
* How efficiently is revenue being generated relative to miles travelled?

2. Service Reliability

Understand whether deliveries are being completed on time and where delays occur.

Key questions:

* What percentage of deliveries are on time?
* Where are late deliveries concentrated?
* Which customers, routes, or facilities experience service issues?
* How much detention occurs during deliveries?

3. Fleet & Fuel Efficiency

Understand how efficiently trucks are being operated.

Key questions:

* How much is being spent on fuel?
* How many miles are being travelled?
* What is the fuel cost per mile?
* What is the fleet’s average MPG?
* How do maintenance costs affect fleet operations?

4. Driver Performance & Safety

Understand driver performance and safety patterns.

Key questions:

* Which drivers have stronger on-time performance?
* How does driver MPG vary?
* Where are safety incidents concentrated?
* How do driver performance metrics change over time?

### 🗂️ Dataset

The project contains 14 related tables covering different areas of the transportation operation.

|Table | Purpose |
|---|---|
| `drivers` | Driver demographics, employment and license information |
| `trucks` | Fleet equipment and truck information |
| `trailers` | Trailer inventory and status |
| `customers` | Customer accounts and contract information |
| `facilities` | Terminal and warehouse locations |
| `routes` | Origin-destination routes and distance information |
| `loads` | Shipment, revenue and booking information |
| `trips` | Actual trip performance and operational information |
| `fuel_purchases` | Fuel transactions and fuel costs |
| `maintenance_records` | Truck maintenance history and costs |
| `delivery_events` | Pickup and delivery events, detention and on-time status |
| `safety_incidents` | Accidents, violations and damage information |
| `driver_monthly_metrics` | Monthly driver performance metrics |
| `truck_utilization_metrics` | Monthly truck utilization metrics |

A detailed data dictionary/key guide is maintained separately from the main README.  
# 🧹 Data Preparation & Cleaning

The source data was initially provided as multiple CSV files.

Power Query was used to prepare the data before loading it into the Power BI model.

Main preparation steps

* Imported the individual CSV tables
* Reviewed table structures and data types
* Validated primary and foreign keys
* Checked column quality
* Investigated missing values
* Checked duplicate identifiers
* Reviewed relationships between tables
* Prepared tables for Power BI modeling
* Validated aggregated monthly metric tables
* Preserved relevant missing values rather than creating artificial information

Data Quality Observations

Some fields contained missing values, particularly within operational relationships such as driver, truck and trailer identifiers.

Approximately 2% of trip records contained missing driver, truck or trailer identifiers.

These records were retained because the missing values represent a limitation in the source dataset rather than values that should be artificially replaced.

The project also identified fields with little or no variation. For example, load_status and fuel_type contained single-value information in the dataset. These fields were retained but were not treated as meaningful segmentation variables.  
# 🏗️ Data Model

The Power BI model was designed around the relationships between customers, loads, trips, fleet assets and operational events.

The major relationship structure is: 
```
Customers ──────┐
                │
Routes ─────────┤
                ▼
              Loads
                │
                ▼
              Trips
          ┌─────┼─────┬───────────┐
          ▼     ▼     ▼           ▼
       Drivers Trucks Trailers   Fuel Purchases
          │      │
          │      ├──────────> Maintenance Records
          │      └──────────> Truck Utilization Metrics
          │
          └──────────────> Driver Monthly Metrics

Trips ───────────────> Delivery Events ───────> Facilities

Trips ───────────────> Safety Incidents
```

### Main Relationships

* Customers 1 → many Loads
* Routes 1 → many Loads
* Loads 1 → 1 Trips
* Drivers 1 → many Trips
* Trucks 1 → many Trips
* Trailers 1 → many Trips
* Drivers 1 → many Driver Monthly Metrics
* Trucks 1 → many Truck Utilization Metrics
* Trucks 1 → many Maintenance Records
* Trips 1 → many Fuel Purchases
* Trips 1 → many Safety Incidents
* Trips 1 → many Delivery Events
* Facilities 1 → many Delivery Events

Most relationships use single-direction filtering to keep the model predictable and avoid unnecessary ambiguity.

# 📐 Key Measures & DAX

More than 25 DAX measures were created to support the analysis.

Some of the main measures include:

Total Revenue

Revenue combines the relevant revenue components recorded in the Loads table, including base load revenue, fuel surcharge and accessorial revenue.

#### Total Revenue =
SUMX(loads,loads[revenue]+loads[fuel_surcharge]+loads[accessorial_revenue])

#### Total Loads
Total Loads = DISTINCTCOUNT(loads[load_id])

#### Revenue per Mile 
Revenue per Mile = DIVIDE([Total Revenue],[Total Miles],0)

#### On-Time Delivery %

The calculation focuses specifically on delivery events and uses the Boolean On_Time_Flag.
On-Time Delivery % = DIVIDE(CALCULATE(COUNTROWS(delivery_events),delivery_events[Event_Type] = "Delivery" delivery_events[On_Time_Flag] = TRUE()
    ),
    CALCULATE(COUNTROWS(delivery_events),delivery_events[Event_Type] = "Delivery"
    ),
)

#### Total Fuel Cost 
Total Fuel Cost = SUM(fuel_purchases[fuel_cost])

#### Total Fuel Gallons 
Total Fuel Gallons = SUM(fuel_purchases[fuel_purchase_gallons])

#### Fuel Cost per Mile 
Fuel Cost per Mile = DIVIDE([Total Fuel Cost],[Total Miles],
)

#### Fleet Average MPG 
Fleet Average MPG = DIVIDE([Total Miles],[Total Fuel Gallons],
)

#### Total Maintenance Cost 
Total Maintenance Cost = SUM(maintenance_records[maintenance_cost])

#### Total Safety Incidents
Total Safety Incidents = DISTINCTCOUNT(safety_incidents[incident_id])

#### Driver Monthly Measures

Average Driver On-Time Rate 
Avg Driver On-Time Rate = AVERAGE(driver_monthly_metrics[on_time_delivery_rate]
)

#### Reported Average Idle Hours 
Sum of Reported Avg Idle Hours = SUM(driver_monthly_metrics[average_idle_hours]
)

# 📅 Time Intelligence

A dedicated Date table was created to support time-based analysis.

The Date table includes:

* Date
* Month
* Month Number
* Quarter
* Year

Time-intelligence measures were developed for revenue analysis, including year-over-year comparisons. 

#### Revenue Last Year 
Revenue Last Year =CALCULATE([Total Revenue],SAMEPERIODLASTYEAR('Date'[Date])
)

#### Revenue YoY % 
Revenue YoY % = DIVIDE([Total Revenue] - [Revenue Last Year],[Revenue Last Year],
)

# 📊 Dashboard Structure

The final Power BI report is organized into four dashboards.

#### Executive Overview

Business Question

How is the business performing overall?

Headline KPIs

* Total Revenue
* Total Loads
* Revenue per Mile
* On-Time Delivery %
* Revenue YoY %

Analysis

* Revenue trend over time
* Revenue by customer
* Revenue by customer type
* Revenue by route
* Revenue relative to distance travelled

Dashboard Preview
