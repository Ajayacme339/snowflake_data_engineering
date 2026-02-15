# Snowflake S3 Data Load — Menu Dataset

## 📌 Overview
This project demonstrates how to load structured and semi-structured data from an Amazon S3 bucket into Snowflake, create a table, and run analytical queries on the dataset.

The workflow covers:

- Setting up Snowflake account context (role, database, warehouse, schema)
- Creating a table for menu data
- Creating an external stage pointing to S3
- Loading data using `COPY INTO`
- Running validation and exploratory queries
- Querying semi-structured JSON data using `VARIANT` and `FLATTEN`

This is a great example for learning Snowflake data ingestion and querying patterns.

---

## 🏗️ Architecture / Workflow

1. Configure Snowflake environment
2. Create target table (`MENU`)
3. Create external stage pointing to S3
4. Load CSV files into Snowflake
5. Validate row counts
6. Run analytical queries
7. Extract nested JSON fields

---

## 📂 Dataset Source

Data is loaded from the public S3 bucket:



---

## ⚙️ Setup Steps

### 1️⃣ Set Account Context

```sql
USE ROLE ACCOUNTADMIN;
USE DATABASE SNOWFLAKE_LEARNING_DB;
USE WAREHOUSE SNOWFLAKE_LEARNING_WH;
USE SCHEMA AKUMAR2104_LOAD_SAMPLE_DATA_FROM_S3;



Create MENU Table
CREATE OR REPLACE TABLE MENU
(
    menu_id NUMBER(19,0),
    menu_type_id NUMBER(38,0),
    menu_type VARCHAR,
    truck_brand_name VARCHAR,
    menu_item_id NUMBER(38,0),
    menu_item_name VARCHAR,
    item_category VARCHAR,
    item_subcategory VARCHAR,
    cost_of_goods_usd NUMBER(38,4),
    sale_price_usd NUMBER(38,4),
    menu_item_health_metrics_obj VARIANT
);

3️⃣ Create External Stage
CREATE OR REPLACE STAGE blog_stage1 
URL = 's3://sfquickstarts/tastybytes/'
FILE_FORMAT = (TYPE = CSV);


List files:

LIST @blog_stage1/raw_pos/menu/;

4️⃣ Load Data into Snowflake
COPY INTO MENU 
FROM @blog_stage1/raw_pos/menu/;

🔎 Validation Queries
Row Count
SELECT COUNT(*) AS ROW_COUNT FROM MENU;

Preview Data
SELECT * FROM MENU LIMIT 20;

📊 Example Analysis Queries
Menu Items for "Freezing Point" Truck
SELECT menu_item_name
FROM MENU
WHERE truck_brand_name = 'Freezing Point';

Extract Ingredients from Semi-Structured Column
SELECT
    m.menu_item_name,
    obj.value:"ingredients"::ARRAY AS ingredients
FROM MENU m,
    LATERAL FLATTEN (
        input => m.menu_item_health_metrics_obj:menu_item_health_metrics
    ) obj
WHERE truck_brand_name = 'Freezing Point'
AND menu_item_name = 'Mango Sticky Rice';


This demonstrates how to query nested JSON using Snowflake’s VARIANT type.

🧠 Key Concepts Demonstrated

Snowflake roles and context management

External stages with S3

Bulk loading with COPY INTO

Working with CSV file formats

Semi-structured data handling

LATERAL FLATTEN

JSON extraction

Data validation

🚀 Use Cases

Data engineering learning projects

Snowflake ingestion tutorials

Analytics sandbox

Interview preparation

Data pipeline demos

🛠️ Requirements

Snowflake account

Appropriate permissions (ACCOUNTADMIN or equivalent)

Access to public S3 bucket
