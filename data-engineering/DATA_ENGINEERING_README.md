# Data Engineering --- Real-World Architecture

This section documents my learning and understanding of **modern Data
Engineering architecture** used in real-world enterprise environments.

The purpose is to understand how data moves from operational/source
systems into analytical systems such as Data Warehouses and BI
platforms.

> **Note:** This is a reference/learning architecture. Different
> companies use different tools and may have additional or fewer
> components.

------------------------------------------------------------------------

## Architecture

![Modern Data & Analytics
Architecture](../architectures/azure-modern-data-analytics-architecture.png)

### High-Level Data Flow

``` text
ERP / Source Systems
        ↓
ETL / ELT
(Azure Data Factory)
        ↓
Staging
        ↓
Data Warehouse
        ↓
Semantic Layer
        ↓
Power BI
```

The Data Engineering part mainly focuses on:

``` text
Source Systems
      ↓
ETL / ELT
      ↓
Staging
      ↓
Data Warehouse
```

The data is then consumed by the Semantic Layer and BI/reporting tools.

------------------------------------------------------------------------

# 1. Source Systems / ERP

ERP stands for **Enterprise Resource Planning**.

ERP and other operational systems generate the original business data.

Examples:

-   Sales
-   Customers
-   Products
-   Orders
-   Inventory
-   Finance
-   Suppliers
-   Employees

Example source data:

``` text
Order_ID | Customer_ID | Product | Quantity | Price | Order_Date
-----------------------------------------------------------------
1001     | C101        | Laptop  | 2        | 50000 | 2026-08-01
1002     | C102        | Mouse   | 5        | 1000  | 2026-08-02
```

These systems are mainly designed to run business operations. They are
not usually optimized for large-scale analytical queries.

The Data Engineer's responsibility is to bring this data into an
environment where it can be processed and analyzed efficiently.

------------------------------------------------------------------------

# 2. ETL / ELT --- Azure Data Factory

**Azure Data Factory (ADF)** can be used to build and orchestrate data
pipelines.

A pipeline can automate the movement and processing of data from source
systems to analytical storage.

Typical responsibilities include:

-   Extracting data
-   Moving data between systems
-   Scheduling pipelines
-   Transforming data
-   Loading data
-   Monitoring pipeline execution
-   Handling failures
-   Running incremental loads

## ETL

ETL means:

``` text
Extract → Transform → Load
```

The data is transformed before it is loaded into the target system.

Example:

``` text
ERP
 ↓
Extract
 ↓
Clean / Transform
 ↓
Load
 ↓
Warehouse
```

## ELT

ELT means:

``` text
Extract → Load → Transform
```

The data is first loaded into the analytical environment and
transformations are performed there.

Example:

``` text
ERP
 ↓
Extract
 ↓
Load
 ↓
Data Warehouse
 ↓
Transform using SQL
```

Modern cloud data platforms commonly support both approaches.

------------------------------------------------------------------------

# 3. Staging Layer

The **Staging Layer** is an intermediate area between source systems and
the Data Warehouse.

A simplified flow is:

``` text
ERP
 ↓
ADF
 ↓
Staging
 ↓
Data Warehouse
```

The staging layer can be used for:

-   Initial data ingestion
-   Data validation
-   Data type checking
-   Deduplication
-   Transformation preparation
-   Incremental loading
-   Temporary storage
-   Handling incoming data before warehouse processing

### Example

Suppose the source contains:

``` text
Customer_ID | Name       | Age | City
---------------------------------------
101         | Dhruv      | 21  | Ahmedabad
102         | Rahul      | 25  | Surat
102         | Rahul      | 25  | Surat
103         | Priya      | xx  | Mumbai
```

There are potential data-quality problems:

-   Duplicate customer
-   Invalid age
-   Possible missing or inconsistent values

The pipeline can identify and handle these issues before the data
becomes part of the analytical warehouse.

------------------------------------------------------------------------

# 4. Data Warehouse

The **Data Warehouse** stores structured and historical data designed
for analytics and reporting.

A Data Warehouse is different from an operational ERP database because
it is designed primarily for analytical workloads.

Common characteristics include:

-   Historical data
-   Structured analytical models
-   Fact tables
-   Dimension tables
-   Business-friendly data structures
-   Optimized analytical queries

------------------------------------------------------------------------

# 5. Fact and Dimension Tables

A common Data Warehouse modelling approach is the **Star Schema**.

Example:

``` text
                    Dim_Customer
                         |
                         |
Dim_Product ---- Fact_Sales ---- Dim_Date
                         |
                         |
                    Dim_Store
```

## Fact Table

A fact table generally stores measurable business events.

Example:

``` text
Fact_Sales

Sale_ID
Customer_ID
Product_ID
Date_ID
Store_ID
Quantity
Sales_Amount
```

Examples of measures:

-   Quantity
-   Revenue
-   Sales Amount
-   Cost
-   Profit

## Dimension Table

Dimension tables provide descriptive information about the business
entities.

Example:

``` text
Dim_Customer

Customer_ID
Customer_Name
City
State
Country
```

Another example:

``` text
Dim_Product

Product_ID
Product_Name
Category
Brand
```

This modelling approach makes analytical queries and reporting easier to
manage.

------------------------------------------------------------------------

# 6. Data Quality

Data Engineers are responsible for building reliable data pipelines.

Common data-quality checks include:

### Missing values

``` text
Customer_ID = NULL
```

### Duplicate records

``` text
Customer_ID = 102
Customer_ID = 102
```

### Invalid data types

``` text
Age = "twenty"
```

when the column should contain numeric values.

### Invalid dates

``` text
Order_Date = "ABC"
```

### Unexpected values

For example:

``` text
Quantity = -500
```

when negative quantities are not valid for the business case.

The exact validation rules depend on the organization's requirements.

------------------------------------------------------------------------

# 7. Incremental Loading

A Data Engineer does not always need to reload the entire dataset every
time.

For example:

``` text
Previous data:
10,000,000 records

New data:
50,000 records
```

Instead of processing all 10 million records again, an incremental
pipeline may process only the new or changed records.

``` text
Source
  ↓
Identify new/changed records
  ↓
Load only required data
  ↓
Warehouse
```

This can reduce:

-   Processing time
-   Compute cost
-   Network usage
-   Pipeline duration

------------------------------------------------------------------------

# 8. Pipeline Scheduling

Data pipelines often run automatically according to a schedule or
trigger.

For example:

``` text
01:00 AM
   ↓
ADF Pipeline starts
   ↓
Extract ERP data
   ↓
Load staging
   ↓
Transform data
   ↓
Update warehouse
   ↓
Pipeline completed
```

A production pipeline may also include:

-   Error handling
-   Retry logic
-   Monitoring
-   Alerts
-   Logging
-   Dependency management

------------------------------------------------------------------------

# 9. Data Engineer Responsibilities

A Data Engineer is responsible for much more than simply moving data.

Typical responsibilities include:

### Data Ingestion

``` text
Source Systems → Data Platform
```

### Data Transformation

``` text
Raw Data
   ↓
Clean
   ↓
Validate
   ↓
Transform
```

### Data Storage

Designing and maintaining:

-   Data Lakes
-   Data Warehouses
-   Staging areas
-   Data marts

depending on the company's architecture.

### Data Modelling

Working with:

-   Fact tables
-   Dimension tables
-   Star schemas
-   Relationships

### Data Quality

Ensuring data is:

-   Accurate
-   Consistent
-   Complete
-   Valid
-   Reliable

### Pipeline Management

Building and maintaining:

-   ETL/ELT pipelines
-   Scheduled jobs
-   Incremental loads
-   Error handling
-   Monitoring

### Performance and Reliability

Making pipelines and data systems efficient, stable, and maintainable.

------------------------------------------------------------------------

# 10. Data Engineer vs Data Analyst

There can be overlap between roles, but their main focus is different.

  Data Engineer                Data Analyst
  ---------------------------- -------------------------
  Builds data pipelines        Analyzes data
  Ingests source data          Writes analytical SQL
  Builds data infrastructure   Creates reports
  Transforms data              Builds dashboards
  Maintains data warehouse     Defines KPIs
  Handles data quality         Finds business insights
  Pipeline monitoring          Data visualization
  Orchestration                Power BI / Tableau
  Production data systems      Business analysis

Some organizations also have an **Analytics Engineer** or **BI
Developer** role between these areas.

------------------------------------------------------------------------

# 11. Technologies Can Change

The architecture concept is more important than a specific tool.

For example, one company may use:

``` text
Azure Data Factory
        ↓
Azure/Synapse Data Warehouse
        ↓
Power BI
```

Another company might use:

``` text
Airflow
   ↓
Snowflake
   ↓
dbt
   ↓
Tableau
```

Another might use:

``` text
Fivetran
   ↓
BigQuery
   ↓
dbt
   ↓
Looker
```

The technologies are different, but the fundamental idea can remain:

``` text
Source
  ↓
Ingest
  ↓
Store
  ↓
Transform
  ↓
Model
  ↓
Analyze
  ↓
Visualize
```

------------------------------------------------------------------------

# 12. What I Learned From This Architecture

Through studying this architecture, I understood that Data Engineering
provides the foundation for analytics.

A Data Analyst may work with a Power BI dashboard and see:

``` text
Revenue = ₹50 Crore
```

But behind that number there may be a complete data pipeline:

``` text
ERP
 ↓
ADF
 ↓
Staging
 ↓
Data Warehouse
 ↓
Fact / Dimension Model
 ↓
Semantic Layer
 ↓
DAX / KPI
 ↓
Power BI
```

Understanding this complete flow helps explain where analytical data
comes from and how it becomes trustworthy enough for business decisions.

------------------------------------------------------------------------

# Key Takeaway

The main idea I learned is:

> **Data Engineers build the reliable data foundation that allows
> analysts, BI developers, and business teams to work with trustworthy
> data.**

The exact tools vary between organizations, but the core data
engineering principles remain similar.

This repository is part of my ongoing learning journey to understand
**real-world data engineering, analytics architecture, and modern data
platforms**, and to share my notes with others who are learning the same
concepts.
