# Olympics Data Engineering Project

## Project Overview
This end-to-end data engineering project demonstrates a robust pipeline designed for analyzing Olympics data using Azure services. I tried to create a seamless flow from raw data ingestion to structured analytics, and it can underscore the practical application of data engineering principles and provide a scalable foundation for final visualization insights. 

## Tools and Technologies
- **Cloud Platform**: Azure
- **Data Storage**: Azure Data Lake Storage Gen2
- **Data Processing**: Azure Databricks (PySpark)
- **Orchestration**: Azure Data Factory
- **Querying and Analytics**: Azure Synapse Analytics (SQL)
- **Data Modeling**: Raw, Transformed, and Analytical layers

## Considerations?
- **Handling Complex Transformations**: It Includes transformations of nested data structures, aggregations, and multi-level joins using PySpark and SparkSQL, preparing data for easy analytics.
- **Scalable and Modular Pipeline Design**: Supports both incremental and full load processing, ensuring scalability and data consistency.
- **Analytics-Ready Data Models**: The data is organized into clear, efficient data layers, optimized for BI tool integration and fast querying.
- **End-to-End Orchestration**: From ingestion to analytics, every step is orchestrated with Azure Data Factory, automating the flow of data reliably across the pipeline.

## Pipeline Structure
1. **Data Ingestion**: Data is ingested from an HTTP server into Azure Data Lake Storage Gen2 using Azure Data Factory, forming the raw layer.
2. ## Trigger Scheduling in Azure Data Factory
To ensure timely data processing, triggers I configured in Azure Data Factory for automatic pipeline execution at specific intervals:
- **Incremental Load Trigger**: An event-based trigger initiates incremental loads whenever new data is detected in the source, minimizing redundancy and optimizing processing time.
- **Weekly Aggregation Trigger**: For heavy transformations, a weekly trigger handles aggregation and analytics layers, preparing data for BI reporting and dashboards.

3. **Sample Data Transformation in Databricks**:
   - **PySpark Transformations**:
     - Top Gold Medal Countries:
       ```python
       top_gold_medal_countries = Medals.orderBy("Gold", ascending=False).select("TeamCountry", "Gold").show()
       ```
     - Average Entries by Gender:
       ```python
       average_entries_by_gender = Entries_Gender.withColumn(
           'Avg_Female', Entries_Gender['Female'] / Entries_Gender['Total']
       ).withColumn(
           'Avg_Male', Entries_Gender['Male'] / Entries_Gender['Total']
       )
       average_entries_by_gender.show()
       ```
   - **Data Export to ADLS Gen2**:
     - Example for Athletes data:
       ```python
       athlete.repartition(1).write.mode("overwrite").option("header", "true").csv("dbfs:/mnt/olympicsview/transformed-data/athletes")
       ```
     - Similar transformations for Coaches, Entries_Gender, Medals, and Teams.

4. **Data Analytics in Synapse**:
   - SQL queries analyze transformed data in Synapse Analytics:
     - **Total Athletes by Country**:
       ```sql
       SELECT Country, COUNT(*) AS total_athletes
       FROM tbl_athletes
       GROUP BY Country
       ORDER BY total_athletes DESC;
       ```
     - **Total Medals by Country**:
       ```sql
       SELECT TeamCountry, SUM(Gold) AS Total_Gold, SUM(Silver) AS Total_Silver, SUM(Bronze) AS Total_Bronze
       FROM tbl_Medals
       GROUP BY TeamCountry
       ORDER BY Total_Gold DESC;
       ```
     - **Average Entries by Gender per Discipline**:
       ```sql
       SELECT Discipline, AVG(Male) AS avg_male, AVG(Female) AS avg_female
       FROM tbl_Entries_Gender
       GROUP BY Discipline;
       ```

## Data Layers
- **Raw Layer**: Stores unprocessed data directly from the source.
- **Transformed Layer**: Contains cleaned and processed data, ready for analytics.
- **Analytical Layer**: Optimized for queries, providing insights like medal counts, athlete demographics, and participation trends.

## Project Benefits
- **Scalable and Modular Design**: Designed to handle large volumes of data efficiently and facilitates reusability across projects.
- **Analytical Ready**: Prepares data for seamless integration with BI tools to support data-driven decision-making.
- **End-to-End Orchestration**: Ensures smooth data flow from ingestion through analytics.

## Future Enhancements
- **Real-Time Processing**: Extend to support real-time data ingestion and processing for live event analysis.
---
