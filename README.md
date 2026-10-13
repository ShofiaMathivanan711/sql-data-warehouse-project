# SQL Data Warehouse Project

## Overview

This project demonstrates the implementation of a modern SQL Server Data Warehouse using a Medallion Architecture (Bronze, Silver, Gold) approach.

The solution ingests data from CRM and ERP source systems, performs data cleansing and standardization, and delivers business-ready dimensional models for reporting, analytics, and downstream consumption.

---

## Project Objectives

- Build a scalable SQL Server Data Warehouse.
- Implement ETL processes using Stored Procedures.
- Apply data quality checks and transformation rules.
- Design a dimensional model using Star Schema.
- Create business-ready views for analytics and reporting.
- Demonstrate end-to-end data lineage from sources to reporting layer.

---

## Architecture

### High-Level Architecture

The project follows a three-layer Medallion Architecture:

- **Bronze Layer** → Raw data ingestion
- **Silver Layer** → Data cleansing and standardization
- **Gold Layer** → Business-ready dimensional model

![High Level Architecture](images/High%20level%20Architecture.png)

---

## Data Sources

The warehouse integrates data from two source systems:

### CRM System
- Customer Information
- Product Information
- Sales Transactions

### ERP System
- Customer Demographics
- Customer Locations
- Product Categories

Source files are provided as CSV files and loaded using SQL Server `BULK INSERT`.

---

## Data Warehouse Layers

### Bronze Layer

Purpose:
- Landing zone for raw source data
- Exact copy of source files
- No transformations applied

Load Strategy:
- Full Load
- Batch Processing
- Truncate & Insert

Tables:

```text
bronze.crm_cust_info
bronze.crm_prd_info
bronze.crm_sales_details

bronze.erp_loc_a101
bronze.erp_cust_az12
bronze.erp_px_cat_g1v2
```

---

### Silver Layer

Purpose:
- Data cleansing
- Standardization
- Normalization
- Business rule application

Transformations performed:

#### Customer Data
- Remove duplicates
- Standardize gender values
- Standardize marital status values
- Trim unwanted spaces

#### Product Data
- Derive category identifiers
- Map product line codes
- Handle missing cost values
- Generate product end dates

#### Sales Data
- Validate dates
- Recalculate invalid sales amounts
- Derive missing prices

#### ERP Data
- Standardize country names
- Handle future birth dates
- Normalize gender values
- Remove invalid prefixes

Tables:

```text
silver.crm_cust_info
silver.crm_prd_info
silver.crm_sales_details

silver.erp_loc_a101
silver.erp_cust_az12
silver.erp_px_cat_g1v2
```

---

### Gold Layer

Purpose:
- Business-ready presentation layer
- Dimensional modeling
- Analytics and reporting

Objects:

```text
gold.dim_customers
gold.dim_products
gold.fact_sales
```

---

## Data Flow

The following diagram shows how data moves from source systems through Bronze and Silver layers before being transformed into analytical models in the Gold layer.

![Data Flow](images/Data%20Flow.png)

---

## Star Schema Design

The Gold Layer implements a Sales Data Mart using a Star Schema.

### Fact Table

#### fact_sales

Measures:
- Sales Amount
- Quantity
- Price

Foreign Keys:
- Customer Key
- Product Key

---

### Dimension Tables

#### dim_customers

Attributes:
- Customer ID
- Customer Number
- First Name
- Last Name
- Country
- Marital Status
- Gender
- Birthdate

#### dim_products

Attributes:
- Product ID
- Product Number
- Product Name
- Category
- Subcategory
- Maintenance
- Cost
- Product Line

---

### Star Schema Diagram

![Star Schema](images/Star%20Schema.png)

---

## ETL Process

### Step 1: Create Database & Schemas

Creates:

```sql
DataWarehouse

bronze
silver
gold
```

---

### Step 2: Load Bronze Layer

Stored Procedure:

```sql
EXEC bronze.load_bronze;
```

Responsibilities:

- Truncate Bronze tables
- Import CSV files
- Load raw source data

---

### Step 3: Load Silver Layer

Stored Procedure:

```sql
EXEC silver.load_silver;
```

Responsibilities:

- Data cleansing
- Data validation
- Standardization
- Business rule application

---

### Step 4: Query Gold Layer

Example:

```sql
SELECT *
FROM gold.fact_sales;
```

```sql
SELECT *
FROM gold.dim_customers;
```

```sql
SELECT *
FROM gold.dim_products;
```

---

## Database Objects

### Schemas

```text
bronze
silver
gold
```

### Stored Procedures

```text
bronze.load_bronze
silver.load_silver
```

### Gold Views

```text
gold.dim_customers
gold.dim_products
gold.fact_sales
```

---

## Technologies Used

- SQL Server
- T-SQL
- Stored Procedures
- BULK INSERT
- Star Schema
- Dimensional Modeling
- Data Warehousing
- ETL Development

---

## Key Concepts Demonstrated

- Data Warehouse Architecture
- Medallion Architecture
- ETL Pipeline Design
- Data Cleansing
- Data Standardization
- Slowly Changing Product History Handling
- Dimensional Modeling
- Star Schema Design
- Fact and Dimension Tables
- Data Lineage

---

## Repository Structure

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
├── images/
│   ├── High level Architecture.png
│   ├── Data Flow.png
│   └── Star Schema.png
│
└── README.md
```

---

## Business Outcome

This project delivers a complete analytics-ready Data Warehouse that transforms raw CRM and ERP data into a structured Sales Data Mart, enabling:

- Business Intelligence Reporting
- Ad-hoc Analysis
- KPI Tracking
- Dashboard Development
- Data Science & Machine Learning Use Cases

---

## Author

**Shofia Mathivanan**

