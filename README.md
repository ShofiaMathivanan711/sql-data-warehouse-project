# SQL Data Warehouse Project

## Overview

This project demonstrates the design and implementation of a modern Data Warehouse using SQL Server and the Medallion Architecture pattern (Bronze, Silver, and Gold layers).

Data is extracted from CRM and ERP source systems, loaded into a raw storage layer, transformed and standardized through an ETL process, and finally exposed as business-ready dimensional models for analytics and reporting.

---

## Project Architecture

The solution follows a multi-layered Data Warehouse architecture:

### Bronze Layer
- Stores raw source data.
- Acts as the landing zone for CRM and ERP files.
- No transformations are applied.
- Supports full-load batch processing.

### Silver Layer
- Stores cleansed and standardized data.
- Applies business rules and validations.
- Removes duplicates.
- Normalizes values and handles missing data.
- Creates a consistent data foundation.

### Gold Layer
- Provides business-ready datasets.
- Implements a Star Schema model.
- Contains dimension and fact views.
- Supports reporting, dashboards, analytics, and machine learning workloads.

---

## Data Sources

### CRM System
Source data files:

- Customer Information
- Product Information
- Sales Details

### ERP System
Source data files:

- Customer Demographics
- Customer Locations
- Product Categories

All source data is loaded from CSV files using SQL Server BULK INSERT operations.

---

## Data Warehouse Layers

## Bronze Layer

The Bronze layer stores raw data exactly as received from source systems.

### Tables

```sql
bronze.crm_cust_info
bronze.crm_prd_info
bronze.crm_sales_details

bronze.erp_loc_a101
bronze.erp_cust_az12
bronze.erp_px_cat_g1v2
```

### Characteristics

- Raw source data
- No transformations
- Truncate and reload strategy
- Full batch loads
- Source system preservation

---

## Silver Layer

The Silver layer performs data cleansing, validation, standardization, and enrichment.

### Tables

```sql
silver.crm_cust_info
silver.crm_prd_info
silver.crm_sales_details

silver.erp_loc_a101
silver.erp_cust_az12
silver.erp_px_cat_g1v2
```

### Transformations Performed

#### Customer Transformations

- Removed duplicate customer records.
- Retained latest customer record.
- Standardized gender values.
- Standardized marital status values.
- Trimmed leading and trailing spaces.

#### Product Transformations

- Extracted category identifiers.
- Extracted product keys.
- Standardized product line values.
- Replaced NULL costs with zero.
- Calculated product end dates using window functions.

#### Sales Transformations

- Validated order, ship, and due dates.
- Corrected invalid sales amounts.
- Derived missing prices.
- Handled invalid values and data quality issues.

#### ERP Transformations

- Standardized gender values.
- Removed invalid customer prefixes.
- Standardized country names.
- Replaced missing countries with default values.
- Removed future birth dates.

---

## Gold Layer

The Gold layer provides business-ready analytical models.

### Views

```sql
gold.dim_customers
gold.dim_products
gold.fact_sales
```

### Purpose

- Reporting
- Business Intelligence
- Dashboarding
- Ad-hoc Analysis
- Analytical Queries
- Machine Learning

---

# Star Schema

The Gold layer implements a Sales Data Mart using a Star Schema design.

---

## Dimension: dim_customers

Contains customer-related attributes.

### Columns

```text
customer_key
customer_id
customer_number
first_name
last_name
country
marital_status
gender
birthdate
create_date
```

### Business Rules

- CRM is treated as the primary source for gender information.
- ERP data is used as a fallback source.
- Customer location information is enriched from ERP source tables.

---

## Dimension: dim_products

Contains product-related attributes.

### Columns

```text
product_key
product_id
product_number
product_name
category_id
category
subcategory
maintenance
cost
product_line
start_date
```

### Business Rules

- Category information is enriched from ERP category tables.
- Historical products are excluded.
- Only active product records are exposed.

---

## Fact: fact_sales

Contains transactional sales facts.

### Columns

```text
order_number
product_key
customer_key
order_date
shipping_date
due_date
sales_amount
quantity
price
```

### Measures

```text
sales_amount
quantity
price
```

### Relationships

```text
fact_sales → dim_customers
fact_sales → dim_products
```

---

# ETL Process

## Step 1: Create Database and Schemas

Creates the following objects:

```sql
DataWarehouse

bronze
silver
gold
```

---

## Step 2: Load Bronze Layer

Stored Procedure:

```sql
EXEC bronze.load_bronze;
```

Responsibilities:

- Truncate Bronze tables.
- Load CSV source files.
- Preserve raw source data.

---

## Step 3: Load Silver Layer

Stored Procedure:

```sql
EXEC silver.load_silver;
```

Responsibilities:

- Data cleansing.
- Data validation.
- Standardization.
- Data enrichment.
- Business rule implementation.

---

## Step 4: Consume Gold Layer

Example Queries:

```sql
SELECT *
FROM gold.dim_customers;
```

```sql
SELECT *
FROM gold.dim_products;
```

```sql
SELECT *
FROM gold.fact_sales;
```

---

# Data Lineage

```text
CRM CSV Files
        |
        v
Bronze CRM Tables
        |
        v
Silver CRM Tables
        |
        v
Gold Dimensions & Facts

ERP CSV Files
        |
        v
Bronze ERP Tables
        |
        v
Silver ERP Tables
        |
        v
Gold Dimensions
```

---

# Database Objects

## Schemas

```sql
bronze
silver
gold
```

### Bronze Tables

```sql
crm_cust_info
crm_prd_info
crm_sales_details

erp_loc_a101
erp_cust_az12
erp_px_cat_g1v2
```

### Silver Tables

```sql
crm_cust_info
crm_prd_info
crm_sales_details

erp_loc_a101
erp_cust_az12
erp_px_cat_g1v2
```

### Gold Views

```sql
dim_customers
dim_products
fact_sales
```

### Stored Procedures

```sql
bronze.load_bronze
silver.load_silver
```

---

# Technologies Used

- SQL Server
- T-SQL
- Stored Procedures
- BULK INSERT
- Data Warehousing
- ETL Development
- Dimensional Modeling
- Star Schema
- Window Functions
- Data Quality Validation

---

# Key Features

- End-to-end ETL pipeline
- Medallion Architecture implementation
- Data standardization and cleansing
- Business rule enforcement
- Data lineage tracking
- Star Schema design
- Surrogate key generation
- Analytical data modeling
- Reporting-ready datasets

---

# Repository Structure

```text
sql-data-warehouse-project/
│
├── datasets/
│   ├── source_crm/
│   └── source_erp/
│
├── scripts/
│   ├── create_database.sql
│   ├── bronze_layer.sql
│   ├── silver_layer.sql
│   └── gold_layer.sql
│
├── docs/
│   ├── architecture
│   ├── data_flow
│   └── star_schema
│
└── README.md
```

---

# Business Value

This project transforms raw CRM and ERP data into a centralized Sales Data Mart that enables:

- Business Intelligence Reporting
- Self-Service Analytics
- KPI Monitoring
- Executive Dashboards
- Data Science Workloads
- Machine Learning Applications

---


