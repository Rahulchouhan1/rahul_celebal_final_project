# 🚀 Azure Data Factory: Conditional ETL Pipeline – Customer & Product Data

## 📌 Problem Statement

Design a **dynamic and conditional data pipeline** in **Azure Data Factory (ADF)** that:

1. 💼 Copies **Customer Data** from a database to **Azure Data Lake Storage Gen2 (ADLS)** only if the number of customer records is **greater than 500**.
2. 🔄 If the condition is met, the pipeline triggers a **child pipeline** that:
   - Copies **Product Data** to ADLS
   - But **only if customer count > 600**
3. 🧮 Customer count must be passed from the parent pipeline to the child pipeline via **pipeline parameters**.

---

## 🧠 Logic & Design Architecture

### 📍 Parent Pipeline Responsibilities

- 🔎 **Lookup Activity**
  - Fetch customer count from source database

- 📥 **Set Variable**
  - Store the fetched count

- 🧮 **If Condition**
  - Check if `customerCount > 500`
  - If true:
    - 🛠️ **Copy Data**: Move customer data to ADLS
    - 🚀 **Execute Pipeline**: Trigger child pipeline, passing `customerCount` as a parameter

### 📍 Child Pipeline Responsibilities

- 📤 Accepts `customerCount` as a **parameter**
- ✅ **If Condition** checks if `customerCount > 600`
- 🛠️ **Copy Data** moves **Product Data** to ADLS if condition is satisfied

---

## 🧱 Key ADF Components Used

| Component            | Purpose                                                  |
|---------------------|-----------------------------------------------------------|
| 🔎 Lookup Activity   | Retrieves customer count from the source table            |
| 📥 Set Variable      | Stores retrieved count in a pipeline-level variable       |
| 🧮 If Condition      | Applies logic to conditionally execute activities         |
| 📂 Copy Data         | Transfers data to Azure Data Lake Storage Gen2            |
| 🚀 Execute Pipeline  | Triggers child pipeline and passes parameters             |
| 🎯 Pipeline Parameter| Enables inter-pipeline communication with dynamic values  |

---

## 🗂️ Data Storage

All output data is stored in structured folders in **ADLS Gen2**, partitioned for downstream processing and analytics.

---

