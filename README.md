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


