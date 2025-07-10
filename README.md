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

## 🏗️ Step-by-Step Pipeline Creation

---

### 🔷 Step 1: Create the Parent Pipeline (`CopyCustomerData_Pipeline`)

#### 🔹 1. Lookup Activity
- **Purpose**: Retrieve customer count
- **Query**:
  ```sql
  SELECT COUNT(*) as totalCount FROM Customer
  ```

#### 🔹 2. Set Variable
- **Purpose**: Store the count from lookup
- **Value Expression**:
  ```json
  @activity('GetCustomerCount').output.firstRow.totalCount
  ```

#### 🔹 3. If Condition – `CustomerCount > 500`
- **Expression**:
  ```json
  @greater(variables('CustomerCount'), 500)
  ```

✅ Inside the True block:
- 🗃️ **Copy Data** → Copy **Customer** table to ADLS  
- 🚀 **Execute Pipeline** → Call `ChildPipeline_ProductCopy`  
  - Pass parameter:
    ```json
    @variables('CustomerCount')
    ```

---

### 🔷 Step 2: Create the Child Pipeline (`ChildPipeline_ProductCopy`)

#### 🔹 1. Add Pipeline Parameter
- **Name**: `customerCount`
- **Type**: `Int`

#### 🔹 2. If Condition – `CustomerCount > 600`
- **Expression**:
  ```json
  @greater(int(pipeline().parameters.customerCount), 600)
  ```

✅ Inside the True block:
- 🗃️ **Copy Data** → Copy **Product** table to ADLS

---

### 🔷 Step 3: Export and Deploy

- 📤 **Export ARM Template**:  
  Go to **Manage > ARM Template > Export**

- 🗂️ **Upload to GitHub**:  
  Place exported templates in:  
  ```
  /arm_templates/
  ```

---
## ✅ Sample Output (from Execution)

---

### 🟢 Parent Pipeline – `CopyCustomerData_Pipeline`

- **Customer Count Retrieved**: `1000`
- **CopyCustomerData Activity** → ✅ **Succeeded**
  - Rows Copied: `1000`
- **ExecutePipeline Activity** → ✅ **Child Pipeline Triggered Successfully**

---

### 🟢 Child Pipeline – `ChildPipeline_ProductCopy`

- **Parameter Received**: `customerCount = 1000`
- **Condition (customerCount > 600)** → ✅ **True**
- **CopyProductData Activity** → ✅ **Succeeded**
  - Rows Copied: `1000`

---
## 📁 Repository Structure

```bash
ADF-Customer-Product-Pipeline/
├── arm_templates/                      # Deployment artifacts for Azure
│   ├── factory/
│   │   ├── CopyCustomerData_Pipeline.json       # Parent pipeline definition
│   │   ├── ChildPipeline_ProductCopy.json       # Child pipeline definition
│   ├── linkedTemplates/                         # Any nested ARM templates (if applicable)
│   ├── ARMTemplateForFactory.json               # Factory-wide ARM export
│   └── ARMTemplateParametersForFactory.json     # Parameters for deployment
├── sample_data/                       # Sample input data
│   ├── mock_customer_data.csv
│   └── mock_product_data.csv
├── screenshots/                       # Visual references
│   ├── pipeline_flow.png
│   └── output_success.png
└── README.md                          # Project overview and instructions
```

---

## 🚀 Project Overview

This project contains a **dynamic conditional pipeline** architecture:

- ✅ **Parent Pipeline**: Copies customer data **only if record count > 500**
- ✅ **Child Pipeline**: Copies product data **only if customer count > 600**
- ✅ Count passed as parameter between pipelines
- 📂 Destination: Azure Data Lake Storage Gen2

---

## 📄 Files of Interest

| File                                     | Purpose                                      |
|------------------------------------------|----------------------------------------------|
| `CopyCustomerData_Pipeline.json`         | Implements parent flow with lookup, condition, and execute |
| `ChildPipeline_ProductCopy.json`         | Implements child flow with param check and copy |
| `ARMTemplateForFactory.json`             | Full export of ADF pipeline setup            |
| `ARMTemplateParametersForFactory.json`   | Parameter file for deployment flexibility    |
| `mock_customer_data.csv`                 | Sample customer data to test flow            |
| `mock_product_data.csv`                  | Sample product data for conditional copy     |
| `pipeline_flow.png`                      | Visual overview of pipeline logic            |
| `output_success.png`                     | Screenshot of successful debug output        |

---

## 📦 Deployment Instructions

1. Navigate to the Azure Data Factory → **Manage** section
2. Import `ARMTemplateForFactory.json` and parameters JSON
3. Validate linked services, datasets, and triggers
4. Publish the pipeline and test with sample data

---

## 🧪 How to Use

---

### ⚙️ Setup Instructions

1. **Import ARM Templates**  
   Navigate to your Azure Data Factory instance and import the following:
   - `ARMTemplateForFactory.json`
   - `ARMTemplateParametersForFactory.json`

2. **Configure Linked Services**  
   - Set up connections to:
     - 📘 Source SQL Database
     - 🗂️ Azure Data Lake Storage Gen2 (ADLS)

3. **Trigger the Pipeline**  
   - Run the **Parent Pipeline** manually or via a scheduled trigger.

4. **Monitor Execution**  
   - Use the **Monitor tab** to validate activity status and debug results.

---

## 📈 Future Enhancements

- 📝 Add logging to **ADLS folders or SQL audit tables** for traceability
- 📣 Integrate **email/SMS alerts** using Logic Apps for failure notifications
- 🗃️ Add **dynamic file naming** using `utcnow()` timestamp or `GUID` functions
- 🧩 Parameterize **SQL connection strings** and **ADLS paths** for flexible reuse
- 🔒 Add retry policies and fault tolerance using **Until** and **Wait** activities

---

## 🔗 Author & Links

- 👨‍💻 Built by **Rahul Singh Chouhan**
- 🔗 GitHub Repository: [github.com/your-username/ADF-Customer-Product-Pipeline](https://github.com/your-username/ADF-Customer-Product-Pipeline)




