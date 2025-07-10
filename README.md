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

## 🏗️ Step-by-Step Pipeline Creation

### 🔷 Step 1: Create the Parent Pipeline (`CopyCustomerData_Pipeline`)
1. **Lookup Activity**:  
   Query: `SELECT COUNT(*) as totalCount FROM Customer`
2. **Set Variable**:  
   Save the count from lookup:  
   `@activity('GetCustomerCount').output.firstRow.totalCount`
3. **If Condition (CustomerCount > 500)**:  
   Expression:
   ```expression
   @greater(variables('CustomerCount'), 500)
Inside True block:

✅ Add Copy Data → Copy Customer table to ADLS

✅ Add Execute Pipeline → Call ChildPipeline_ProductCopy

Pass parameter:

expression
Copy
Edit
@variables('CustomerCount')
🔷 Step 2: Create the Child Pipeline (ChildPipeline_ProductCopy)
Add a pipeline parameter: customerCount (Int)

If Condition (CustomerCount > 600):
Expression:

expression
Copy
Edit
@greater(int(pipeline().parameters.customerCount), 600)
Inside True block:

✅ Add Copy Data → Copy Product table to ADLS

🔷 Step 3: Export and Deploy
Export ARM templates via Manage > ARM Template > Export

Upload to GitHub under /arm_templates/

✅ Sample Output (from Debug)
🟢 Parent Pipeline
Customer Count: 1000

CopyCustomerData → Succeeded (1000 rows copied)

ExecutePipeline → Child called successfully

🟢 Child Pipeline
Parameter received: customerCount = 1000

Condition > 600 → true

CopyProductData → Succeeded (1000 rows copied)

📂 Project Structure
pgsql
Copy
Edit
ADF-Customer-Product-Pipeline/
├── arm_templates/
│   ├── factory/
│   ├── linkedTemplates/
│   ├── ARMTemplateForFactory.json
│   └── ARMTemplateParametersForFactory.json
├── sample_data/
│   └── mock_customer_data.csv
├── screenshots/
│   ├── pipeline_flow.png
│   └── output_success.png
└── README.md
🧪 How to Use
Import ARM templates into your ADF instance

Configure linked services (SQL + ADLS)

Trigger the parent pipeline

Check Monitor tab for debug results

📈 Future Enhancements
Add logging to ADLS or SQL audit table

Add email/SMS alerts on failure

Add dynamic file naming (utcnow() or GUID-based)

Parameterize DB and ADLS paths for reusability

🔗 Author & Links
👨‍💻 Built by Rahul Singh Chouhan

 
