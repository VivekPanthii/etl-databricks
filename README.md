## 🧱 CRM–ERP Data Pipeline (Delta Live Tables)

### 📘 Overview

This project implements a **Delta Live Tables (DLT)** pipeline in **Databricks** to build an **end-to-end data processing system** for CRM and ERP datasets using the **Medallion Architecture** — Bronze (raw), Silver (cleansed), and Gold (analytics-ready) layers.

It automates ingestion, transformation, and modeling of structured business data from multiple systems — CRM (Customer Relationship Management) and ERP (Enterprise Resource Planning) — into clean, consistent, and analytics-ready tables.

---

### 🏧 Architecture: Medallion Layers

| Layer      | Purpose           | Description                                                                                                |
| ---------- | ----------------- | ---------------------------------------------------------------------------------------------------------- |
| **Bronze** | Raw ingestion     | Ingests CSV files from cloud storage using Auto Loader (cloudFiles).                                       |
| **Silver** | Clean & Transform | Cleanses and standardizes CRM and ERP data with validation, schema casting, deduplication, and enrichment. |
| **Gold**   | Business Modeling | Builds **dimension and fact tables** for analytics — `dim_customers`, `dim_products`, and `fact_sales`.    |

---

### 🗂️ Data Flow Summary

1. **Bronze Layer** — Reads raw CSVs from paths:

   * `/Volumes/workspace/raw_crm_erp/rawvolume/raw_crm_data/...`
   * `/Volumes/workspace/raw_crm_erp/rawvolume/raw_erp_data/...`

   Uses **Auto Loader (cloudFiles)** for schema inference and evolution.

2. **Silver Layer**

   * Cleans, casts, validates, and enriches Bronze data.
   * Handles missing or invalid data.
   * Converts date formats, applies mappings, trims text, and standardizes columns.

3. **Gold Layer**

   * Joins CRM and ERP data to build analytical models:

     * `gold_crm_erp_dim_customers`
     * `gold_crm_erp_dim_products`
     * `gold_crm_erp_fact_sales`
   * Provides business-friendly schema for dashboards and reports.

---

### 🖑 Table Overview

#### 🦏 Bronze Tables

| Table Name                      | Description            |
| ------------------------------- | ---------------------- |
| `bronze_crm_data_cust_info`     | Raw CRM customer data  |
| `bronze_crm_data_prd_info`      | Raw CRM product data   |
| `bronze_crm_data_sales_details` | Raw CRM sales data     |
| `bronze_erp_data_cust_az12`     | Raw ERP customer table |
| `bronze_erp_data_LOC_A101`      | Raw ERP location table |
| `bronze_erp_data_PX_CAT_G1V2`   | Raw ERP category table |

#### 🧈 Silver Tables

| Table Name                      | Description               |
| ------------------------------- | ------------------------- |
| `silver_crm_data_cust_info`     | Cleaned CRM customer info |
| `silver_crm_data_prd_info`      | Cleaned CRM product info  |
| `silver_crm_data_sales_details` | Cleaned CRM sales details |
| `silver_erp_data_cust_az12`     | Cleaned ERP customer data |
| `silver_erp_data_loc_a101`      | Cleaned ERP location data |
| `silver_erp_data_px_cat_g1v2`   | Cleaned ERP category data |

#### 🥇 Gold Tables

| Table Name                   | Description                       |
| ---------------------------- | --------------------------------- |
| `gold_crm_erp_dim_customers` | Dimension table for customers     |
| `gold_crm_erp_dim_products`  | Dimension table for products      |
| `gold_crm_erp_fact_sales`    | Fact table for sales transactions |

---

### ⚙️ Technologies Used

* **Databricks Delta Live Tables (DLT)**
* **Apache Spark (PySpark)**
* **Auto Loader (cloudFiles)** — for incremental ingestion
* **Medallion Architecture** — for data organization
* **Delta Lake** — for ACID transactions & versioning
* **Python (PySpark)** — for transformations

---

### 📁 Project Structure

```
crm_erp_pipeline/
│
├── dlt_pipeline.py        # Main DLT pipeline script
├── README.md              # Documentation (you’re reading this)
│
├── data/
│   ├── raw_crm_data/
│   │   ├── cust_info/
│   │   ├── prd_info/
│   │   └── sales_details/
│   └── raw_erp_data/
│       ├── CUST_AZ12/
│       ├── LOC_A101/
│       └── PX_CAT_G1V2/
│
└── volumes/
    ├── bronze_crm_erp/
    └── silver_crm_erp/
```

---

### 🚀 How to Run the Pipeline in Databricks

1. **Upload the Script**

   * Add `dlt_pipeline.py` to your Databricks workspace under a suitable directory (e.g., `/Workspace/Users/<your_email>/pipelines/`).

2. **Create a New Delta Live Tables Pipeline**

   * Go to **Workflows → Delta Live Tables → Create Pipeline**.
   * Set:

     * **Source:** path to your `dlt_pipeline.py`
     * **Target:** e.g., `bronze_crm_erp`
     * **Storage Location:** `/Volumes/workspace/bronze_crm_erp/bronze_volume`
     * **Pipeline Mode:** Triggered or Continuous (based on need)

3. **Configure Permissions**

   * Ensure the service principal or user has read access to raw data volumes and write access to the bronze/silver/gold paths.

4. **Start the Pipeline**

   * Click **Start** — Databricks will automatically:

     * Create DLT tables.
     * Track dependencies.
     * Handle incremental updates and schema evolution.

5. **Monitor Progress**

   * Use the **DLT Dashboard** for lineage, schema evolution, data quality, and metrics.

---

### 🧩 Example Use Case

Once the pipeline runs successfully:

* Analysts can query the Gold layer tables directly:

  ```sql
  SELECT country, gender, COUNT(*) AS customer_count
  FROM gold_crm_erp_dim_customers
  GROUP BY country, gender;
  ```

* Data Engineers can integrate the Gold tables with BI tools (e.g., Power BI, Tableau, Databricks SQL).

---

### 🧪 Key Highlights

* ✅ Incremental ingestion via Auto Loader
* ✅ Schema evolution handling (`cloudFiles.schemaEvolutionMode = rescue`)
* ✅ Cleansing & standardization at Silver layer
* ✅ Dimensional modeling at Gold layer
* ✅ Time-based enrichment and metadata columns (`DWH_create_date`, `DWH_update_date`)
* ✅ Reusable and extensible structure for new CRM/ERP sources

---

### 📊 Future Enhancements

* Add **data quality expectations** using `@dlt.expect` decorators.
* Implement **slowly changing dimensions (SCD Type 2)** in Gold layer.
* Integrate **dbt for transformations** in Silver layer.
* Add **metadata-driven ingestion** (parameterized paths).
* Schedule **automated DLT pipeline runs** with Databricks Workflows.

---

