# E-Commerce Bronze Ingestion Platform

A governed, end-to-end data ingestion platform built on **Azure Databricks** and **Unity Catalog**, combining batch and real-time streaming pipelines into a single bronze layer, built as a team sprint to simulate a real production data engineering workflow, from infrastructure to dashboard.

## What this project demonstrates

This isn't a tutorial exercise, it's a working platform that ingests real e-commerce data two different ways, handles a live schema change without downtime, and surfaces the results in a dashboard, all under proper data governance.

- **Governed infrastructure**: Unity Catalog (catalog / schema / volumes), secrets managed through an Azure Key Vault-backed secret scope — no credentials ever hardcoded
- **Batch ingestion**: idempotent, re-runnable loads of reference data (customers, products, category mappings) from the [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Streaming ingestion**: real-time order events published to Azure Event Hub and consumed via Spark Structured Streaming, using Event Hub's Kafka-compatible endpoint
- **Live schema evolution**: a new `discount_code` field is introduced mid-stream and absorbed without restarting the pipeline — demonstrating how production streaming systems handle evolving source data
- **Analytics dashboard**: revenue by category, sales by region, and order trends built directly on the bronze layer
- **Real team collaboration**: feature branches, pull requests, and code review across a 2-person team, mirroring how data platforms actually get built in industry

## Architecture

```
                 ┌─────────────────────────┐
   CSV (Kaggle)  │   Batch Ingestion Job    │
  ──────────────▶│  (customers, products,   │──┐
                  │   category mapping)      │  │
                  └─────────────────────────┘  │
                                                 ▼
                                        ┌──────────────────┐
  Synthetic Order  ┌──────────────┐    │   Bronze Layer     │
  Events           │ Azure Event  │    │  (Unity Catalog,   │
 ────────────────▶│     Hub      │───▶│  governed, ADLS-    │
  (real Olist      │ (Kafka API)  │    │  backed, secrets    │
   order data)      └──────────────┘    │  via Key Vault)     │
                     Spark Structured   └──────────────────┘
                     Streaming                    │
                                                    ▼
                                          ┌──────────────────┐
                                          │     Dashboard      │
                                          │  Revenue by cat.,   │
                                          │  sales by state     │
                                          └──────────────────┘
```

## Tech stack

| Layer | Technology |
|---|---|
| Compute & orchestration | Azure Databricks, Databricks Workflows |
| Governance | Unity Catalog, Azure Key Vault-backed secret scopes |
| Storage | Azure Data Lake Storage (ADLS Gen2), Delta Lake |
| Streaming | Azure Event Hub, Spark Structured Streaming (Kafka protocol) |
| Batch | PySpark, Delta Lake |
| Language | Python, SQL |
| Version control | Git, GitHub (feature branches + PR-based workflow) |

## Repository structure

```
ecommerce-databricks-platform/
├── architecture/                    # Architecture diagrams
├── notebooks/
│   ├── 01_setup/                    # Unity Catalog schema, volumes, external locations
│   ├── 02_batch_ingestion/          # CSV → bronze, idempotent, metadata-tracked
│   ├── 03_streaming_ingestion/      # Event Hub producer + Structured Streaming consumer
│   ├── 04_schema_evolution/         # Documented live schema-change demo
│   └── 05_dashboard/                # Revenue and sales analytics
├── docs/
├── sql/
└── README.md
```

## Engineering highlights

A few technical problems solved along the way, worth calling out for anyone reviewing the code:

- **Diagnosed and fixed a non-obvious Kafka client failure** on Databricks Runtime, the platform internally relocates ("shades") Kafka client classes, which silently breaks standard SASL authentication configs unless the shaded class name is used explicitly.
- **Resolved a Unity Catalog governance gap** where storage existed in Azure but wasn't registered as a UC External Location, a common real-world misconfiguration between infrastructure provisioning and catalog governance.
- **Designed schema evolution around a pre-declared nullable schema** rather than reactive error handling, so a new business field (`discount_code`) can appear in the data stream at any time without ever stopping the pipeline — a deliberate, documented architectural choice with tradeoffs explained in `04_schema_evolution`.

## Team
- Yanquiel Arango Gómez
- Ayyub Orujzade

Built collaboratively across infrastructure & governance, batch ingestion, streaming ingestion, and analytics/dashboard roles, with shared code ownership and mandatory peer review on every pull request.

## Dataset

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — ~100k real orders, used for both the batch reference tables and as the source for realistic streaming order events.
