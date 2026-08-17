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


## Key Insights
**Top Performing Departments**
- **Department 92** generated the highest total sales at **483.9M**, followed by Department 95 ($449.3M) and Department 38 ($393.1M).
- Most top performing departments had a consistent transaction count of 6,435, suggesting sales volume differences are driven more by       revenue per transaction than by transaction frequency.
  
**Holiday Impact on Sales**
- Average sales per store-week record were about 7% higher during holiday weeks ($17.035 vs $15.901), confirming a real sales spike during holidays.
- Total holiday-period sales appear lower only because there are far fewer holiday weeks than regular weeks in the dataset - average, not total, is the correct metric for measuring holiday impact.


## Data Quality & Design Decisions

I used **left joins** instead of inner joins when combining the sales, stores, and features tables. Some sales records may have a missing store or features match — simulating late-arriving or incomplete reference data, which is common in real-world pipelines.

An inner join would have silently dropped those rows, since they'd have no match — in a real business context, this would mean **losing actual revenue from the report with no error or warning**. Left joins preserve every sales record regardless of match status, and unmatched rows are explicitly logged into a separate table (`unmatched_sales_log`) for review, instead of disappearing silently.

This ensures the pipeline is **transparent about data quality issues** rather than hiding them, and revenue figures in the final reports remain accurate and complete.
