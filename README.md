# 🚀 Azure Data Factory Conditional ETL Pipeline – Customer & Product Data

## 📌 Problem Statement

Design a dynamic and conditional data pipeline in **Azure Data Factory** that:

1. Copies **Customer data** from a database to **Azure Data Lake Storage (ADLS)** only if the number of customer records is **greater than 500**.
2. If the condition is met, the pipeline should trigger a **child pipeline** that copies **Product data** from the database to ADLS, but **only if the customer count is greater than 600**.
3. The customer count should be passed from the parent to the child pipeline using **pipeline parameters**.

---

## 🧠 Logic & Design Thinking

To build this efficiently and scalably, the following architectural design was applied:

- **Parent Pipeline** handles:
  - Fetching the customer count from a source table
  - Evaluating if the count is greater than 500
  - Copying Customer data conditionally
  - Triggering a **child pipeline** and passing the count

- **Child Pipeline**:
  - Accepts `customerCount` as a parameter
  - Copies Product data **only** if `customerCount > 600`

- All data is saved to **Azure Data Lake Gen2** in folders for downstream use.

### 🧱 Key Components Used:
- **Lookup Activity** – to get customer count
- **Set Variable** – to store count
- **If Condition** – to apply logic (> 500 and > 600)
- **Copy Data** – to perform actual ETL
- **Execute Pipeline** – to call child pipeline
- **Pipeline Parameters** – to pass values between pipelines

---
 
