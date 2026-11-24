---
title: "data pipelines with apache spark declarative pipelines"
layout: post
date: 2026-11-24 22:30
image: /assets/images/markdown.jpg
headerImage: false
tag:
- data engineer
- databricks
- spark structured streaming
- delta lake
- change data feed
category: blog
author: devdiary2203
description: Hai streaming job y hệt nhau, một job restart ngon lành, một job chết với DELTA_INVALID_CDC_RANGE. Truy ngược lại là một edge case nằm ở bước recover batch 0 của Structured Streaming kết hợp startingVersion = latest.
---

this post introduces how databricks supports efficient data engineering by combining optimized storage techniques, unified data governance through unity catalog, lakeflow's integrated capabilities for data ingestion, transformation, and orchestration

data engineering platform overview

data engineering for data intelligence platform:
- connect: ingestion connectors
- apache spark declarative pipelines: accelerated etl development
- jobs: reliable orchestration for analytics and ai

bên dưới có data processing engine: spark + structured streaming

unified governance: unity catalog is centralized data catalog that provides access control, auditing, data lineage, quality monitoring, data discovery, across databricks workspaces

optimized storage: delta lake, parquet, iceberg

=> focus on apache spark declarative pipeline (simplifying batch and streaming etl )

- figure out where the data lives, spread accross both the datalake and the data warehouse
- make it work for bi reporting, data science, machine learning
- support streaming pipelines for new use cases, enabling generative ai, managing orchestration, all at once
- then, version control, ci cd, deployment, infrastructure
- top of that, handling data quality checks, governance, discovery, so complexity
- finally, operational heavy lifting: hand coding backfills, managing dependencies, partitions, checkpointing, retries

# reliable data pipelines are hard to build
- labor intensive development: Slow & Error-Prone Pipelines: Delays data products and insights,slowing business impact.
- operational complexity: Inefficiencies drive downtime, engineering toil, and wasted resources.
- siloed batch and streaming: Adapting to evolving latency, cost, and SLA needs is cumbersome and expensive.

# spark declaratives pipelines: reliable data pipeline made easy
focus on what really matters: writing and managing transformation logic
- no more getting bogged down in orchestration, infrastructure, edge-case operations
- clean, reliable data pipelines
- all delivered with less effort and more confidence

follow the medallion architecture, progressing data through bronze, silver and gole layers 
- batch ingestion: design for large volume of data that arrive all at once
- streaming ingestion: on the other hand, handles data that flows continously in small increments, enabling near realtime processing

medallion architecture:
- bronze: raw data layer in lakehouse, as-is, sometimes with additional metadata columns added to provide context
- silve: cleaning and transformation happen, turning raw data into a more usable and refined table
- gold: represent the final business-level aggregates designed for downstream use cases such as reporting, machine learning, ai, and more

có 2 cách tạo spark declarative pipeline
- workspace menu tạo etl pipeline
- job and pipeline, chọn create => chọn etl pipeline với sql and python

# dataset types trong databricks
các core concepts and components của spark declarative pipelines bao gồm streaming tables materialized views, temporary views function and differ

dataset types có 3 loại chính, cho các mục đích khác nhau:
- streaming tables: streaming or incremental data processing, only new data
- materilized views: records được xử lý theo yêu cầu và trả về kết quả tương ứng với trạng thái hiện tại của data như transform, aggregate, pre-computing slow queries, frequently used computations
- views: construct bảng ảo, dựa vào query trong pipeline: temporary view, view

# streaming table
- incremental processing modes: exactly once processing of files
- efficient data updates: each time a streaming table is refreshed, data added to the source tables is appended to the streaming table
- sql command usage: sql syntax to create streaming tables -- create or refresh streaming table
- streaming read syntax must use from streaming read_files() to enable incremental streaming read with checkpointing
- auto loaded integration: leverage databricks auto loader, track new files 
- no duplicate reads

example syntax: CREATE OR REFRESH STREAMING TABLE statement to create a streaming table called orders_bronze in the 1_bronze_db schema.

The SELECT clause defines which columns you want to pull into the streaming table from your source data.
In the FROM clause:
- The STREAM keyword tells the pipeline to use streaming semantics, meaning it will process new files/or new rows incrementally as they arrive.
The read_files() function points to the location of your source files. It reads the files and returns the contents in a tabular format.

# materialized views
process records as needed to deliver accurate results based on the current state of your streaming tables
- dyanmic query recalculation: query results are recalculated to reflect changes in upstream datasets
- pipeline driven maintenace: automatically created and updated by pipeline
- sql command usage: create or refresh materilized view
- flexible pipeline placement: used anywhere in your pipeline not just in gold layer
- incremental refresh capability: results are incrementally refresh, avoiding a full rebuild when new data arrives, supported on serverless compute
- cost based optimization: incremental refressh is driven by a cost based optimizer to power fast and efficient transformations on serverless compute

example create a materialized view with sql:
```sql
CREATE OR REFRESH MATERIALIZED VIEW 3_gold_db.gold_orders_by_date AS
SELECT
  date(order_timestamp) AS order_date,
  count(*) AS total_daily_orders
FROM 2_silver_db.orders_silver
GROUP BY date(order_timestamp);
```

# Temporary views and views
temporary view: exist only for the duration of a pipeline run
- sql-based intermidiate layer: create temporary view
views: registered as object in unity catalog and persist beyond the run, stored in unity catalog and created using create view

example create temporary view and view


automatic dependency resolution: declarative pipelins is that pipeline dependencies are automatically parsed, ensures data flows correctly from one step to the next
=> example by mermaid