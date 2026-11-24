# Data Warehouse & Business Intelligence

A complete Data Warehouse and Business Intelligence project developed as part of the Business Intelligence course at the University of Calabria.

The project focuses on the design and implementation of an analytical data platform for a road freight transportation company, transforming operational data into structured information for business analysis and decision support.

The system integrates data from multiple sources through an ETL pipeline, organizes them into a relational Data Warehouse, and provides multidimensional analysis through OLAP cubes.

## Project Overview

The goal is to support the analysis of transportation operations, logistics, and fuel consumption, helping the company monitor its performance over time, identify operational trends, and control costs.

The project covers the main stages of a Business Intelligence solution:

- Data extraction, cleaning, and integration.
- Relational Data Warehouse design.
- ETL pipeline implementation.
- Dimensional modeling and Data Mart development.
- OLAP cube definition and multidimensional analysis.

## Architecture

The system follows a layered architecture based on the Data Warehouse Bus approach, integrating operational data into three analytical Data Marts.

```text
CSV Sources
    |
    v
Staging Area
    |
    v
Operational Data Store (ODS)
    |
    v
Data Warehouse
    |
    v
Three Data Marts
    |
    v
OLAP Cubes
    |
    v
Multidimensional Analysis
```

### Data layers

| Layer | Purpose |
|---|---|
| Staging Area | Stores the extracted data before transformation and integration. |
| Operational Data Store (ODS) | Contains cleaned and integrated data while preserving the granularity of the operational sources. |
| Data Warehouse | Organizes integrated data into analytical structures using Star and Snowflake schemas. |
| Data Marts | Provide dedicated analytical areas for transportation reports, itineraries, and fuel consumption. |

The architecture separates operational data management from analytical processing, providing a structured foundation for multidimensional queries.

## Data Marts

The Data Warehouse is organized into three Data Marts, each designed around a specific business area.

### 1. Transportation Reports

Analyzes daily transportation operations, including completed trips, distance traveled, journey duration, vehicle status, driver shifts, and refueling expenses.

Main measures:

- Number of transportation reports.
- Vehicle kilometers traveled.
- Journey duration.

### 2. Itineraries

Supports the analysis of planned routes and transportation logistics.

The Data Mart enables the investigation of loading and unloading locations, traffic distribution, and journey duration according to customer and cargo characteristics.

Main measures:

- Number of itineraries.
- Itinerary duration.

### 3. Fuel Tank Withdrawals

Focuses on fuel consumption from the company's storage tanks.

The analytical model supports the monitoring of fuel quantities withdrawn, associated costs, and comparisons between suppliers.

Main measures:

- Number of withdrawals.
- Quantity of fuel withdrawn.

## ETL Pipeline

The ETL process is implemented using Pentaho Data Integration (PDI) and consists of three main stages.

### Extraction

Operational data is extracted from CSV files using the `TextFileInput` step.

The pipeline applies sorting and duplicate removal through `Sort Rows` and `Unique Rows` before loading the data into MariaDB.

### Transformation

The Staging Area data is cleaned and transformed before being integrated into the ODS.

The main transformations include:

- Standardization of date and timestamp formats.
- Calculation of derived attributes, including journey duration, distance traveled, itinerary duration, and employment age.
- Data validation using `DataValidator`, checking negative values, inconsistent dates, and missing mandatory fields.

### Loading

The transformed data is loaded from the ODS into the Data Warehouse and the three Data Marts.

Dimension and fact tables are populated using joins, merges, and stream lookups.

Bridge tables are used to represent many-to-many relationships, including those between transportation reports and refueling operations, and between fuel withdrawals and storage tanks.

## Dimensional Modeling

The Data Warehouse uses a combination of Star Schema and Snowflake Schema modeling.

Star schemas provide the main structure for analytical queries, while Snowflake extensions are introduced where additional normalization is required, such as the location hierarchy in the Itineraries Data Mart.

The analytical model includes:

- Fact tables containing business measures.
- Dimension tables describing the context of each event.
- Bridge tables for many-to-many relationships.
- Materialized views for frequently used aggregations.

Examples of materialized views include invoice totals aggregated by location and time period.

The database is implemented in MariaDB, with B-Tree indexes used for keys and date attributes. Other index types, including Hash, Full-Text, and Spatial indexes, are considered according to query requirements.

## OLAP Analysis

The Data Marts are exposed through OLAP cubes defined using Mondrian Schema Workbench.

Saiku is used to explore the cubes and perform multidimensional analysis, including drill-down operations.

The analytical model supports investigations such as:

- Transportation activity over time.
- Distance traveled and journey duration across vehicles and drivers.
- Distribution of transportation activity across itineraries and loading/unloading locations.
- Fuel consumption and withdrawal costs.
- Comparisons between suppliers, locations, and time periods.

## Technology Stack

| Technology | Role |
|---|---|
| MariaDB | Relational database for the ODS and Data Warehouse |
| Pentaho Data Integration (PDI) | ETL pipeline development |
| ApricotDB | SQL schema generation |
| Mondrian Schema Workbench | OLAP cube definition |
| Saiku | Multidimensional analysis and drill-down |
| phpMyAdmin | Database administration and inspection |

## Repository Contents

The repository includes the project source files, the technical report, and the supporting documentation.

- `SORGENTI/` — project source files.
- `README.md` — project overview and technical documentation.
- `RELAZIONE PROGETTO BUSINESS INTELLIGENCE.pdf` — detailed project report.

## Authors

- Angelica Porco
- Yago Otero Martinez
- Hanna Andrieieva
