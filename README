# Walmart Sales Pipeline - Azure Data Factory
Built an end-to-end data pipeline for walmart sales dataset using Azure Date Factory, following medallion architecture (Bronze, Silver, Gold layers).
The pipeline ingests raw retail data, storing it in a data lake as the source of the truth, then loads it into an Azure SQL database where its cleaned, transformed 
to answer business questions for stakeholders.
### Business Questions Answered
1- which departments generate the highest sales?
2- how do holidays impact weekly sales performance?


## Architecture

```
CSV Files
    |
    V
Data Lake Storage (Bronze - raw)
    |
    V
Azure Data Factory - Copy Activity
    |
    V
Azure SQL Databse (Staging)
    |
    V
Data Flow - Join, Split, Aggergate (Silver -> Gold)
    |
    V
SQL Reporting Tables (Gold - business - ready)
```

## Tech Stack

- Azure Data Factory
- Azure Storage Account (Data Lake Gen2)
- Azure SQL Database


## Pipeline Tasks
1. **Ingestion** - Uploaded Raw CSVs files (sales, stores, features) into Azure Data Lake Storage as the raw landing zone.
2. **Connectivity** - Created Linked Services to connect Azure Data Factory to both Azure Data Lake Storage and Azure SQL Database.
3. **Load to Staging** - Used Copy Activity to load the 3 raw CSV files into 3 staging tables in Azure SQL Database (sales, stores, features).
4. **Transform** - Build a Data Flow that:
   - Joins the sales, stores, and features tables using left joins to avoid silently dropping unmatched rows
   - Splits and logs any unmatches rows for data quality auditing
   - Removes duplicate columns after joining 
   - Aggregate the cleaned table to answer business questions (sales by department, holiday impact on sales)
5. **Load To Reporting** - Writes the final aggregated results into gold-layer reporting tables in Azure SQL Database.

