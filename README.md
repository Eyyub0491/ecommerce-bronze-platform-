# E-Commerce Data Ingestion Platform

## Overview
This project implements an end-to-end e-commerce data ingestion platform using Azure Databricks. The goal is to demonstrate batch and streaming data ingestion into a governed Bronze layer using Unity Catalog.

The project is developed as part of the SoftServe Databricks Academy checkpoint, covering concepts from Labs 1–3:
*   Databricks development environment and Git collaboration
*   Azure infrastructure and Unity Catalog governance
*   Batch ingestion
*   Streaming ingestion
*   Schema evolution handling
*   Data visualization and dashboard creation

## Architecture
The platform follows a Bronze-layer ingestion architecture:
```text
                     Batch Sources
                          |
        -----------------------------------
        |                                 |
 Customers CSV                      Products CSV
        |                                 |
        -----------------------------------
                          |
                          v
              Bronze Customers Table
              Bronze Products Table

                     Streaming Source

                    Event Hub
                        |
                        |
                Synthetic Order Events
                        |
                        v
                Bronze Orders Table

                        |
                        v

                 Dashboard / Analytics
```

## Technologies Used
* Azure Databricks
* Unity Catalog
* Azure Data Lake Storage Gen2
* Azure Event Hub
* Apache Spark Structured Streaming
* Delta Lake
* GitHub

