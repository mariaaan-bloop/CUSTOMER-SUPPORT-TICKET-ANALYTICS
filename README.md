# Customer Support Ticket Analytics

> *Turning historical customer support ticket data into decision-ready operational insights.*

## Overview

**Customer Support Ticket Analytics** is a Business Intelligence and Data Analytics project that analyzes historical customer support ticket data to understand support demand, operational performance, backlog, and customer satisfaction.

The project uses **4,000 customer support tickets** recorded from **January 1, 2023 to February 4, 2024**. The raw ticket data is prepared using a Python-based ETL pipeline, loaded into a **PostgreSQL data warehouse**, and then connected directly to **Power BI** for interactive dashboard development and business analysis.

The project focuses on ticket volume, support queues, channels, priorities, first response time, resolution time, CSAT, reopened tickets, backlog, and monthly trends.

---

## Objectives & Scope

### Objectives

The project aims to:

1. **Understand ticket demand** by analyzing ticket volume across queues, channels, priorities, and time periods.
2. **Identify operational bottlenecks** by comparing first response time, resolution time, and backlog across queues and priority levels.
3. **Monitor support performance** through First Response Time (FRT) and resolution time metrics.
4. **Analyze customer satisfaction** using CSAT scores and their relationship with response-time bands.
5. **Monitor reopened tickets** to identify areas that may require further investigation.
6. **Provide actionable business recommendations** based on observed patterns in the support operation.

### In Scope

* Historical customer support ticket analysis
* Python-based ETL and data preparation
* Data quality validation
* PostgreSQL data warehouse
* SQL-based business analysis
* Exploratory data analysis (EDA)
* Ticket volume and trend analysis
* Queue, channel, and priority analysis
* First Response Time (FRT) analysis
* Resolution time analysis
* CSAT analysis
* Reopened ticket analysis
* Open backlog analysis
* Power BI dashboard development
* Business insights and recommendations

### Out of Scope

* Real-time customer support monitoring
* Agent-level performance evaluation
* Staffing or workforce capacity analysis
* Predictive ticket routing
* Automated customer response generation
* Root-cause analysis based on customer contact reasons
* Financial or cost analysis
* NLP or sentiment classification
* Predictive modeling

---

## 🛠️ Data Pipeline & Architecture

The project follows a structured data workflow from the raw CSV file to the final Power BI dashboard.

```text
                  RAW DATA
        support-tickets.csv
                  │
                  ▼
          ┌───────────────┐
          │  Python ETL   │
          │    Pandas     │
          └───────┬───────┘
                  │
                  ▼
        Data Transformation
        & Quality Validation
                  │
                  ▼
          ┌────────────────┐
          │   PostgreSQL   │
          │ Data Warehouse │
          └───────┬────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
   SQL Analysis       Power BI
  Business Questions    Dashboard
        │                   │
        └─────────┬─────────┘
                  ▼
          Business Insights
          & Recommendations
```

### ETL Process

Python is used to prepare the raw customer support ticket data before loading it into PostgreSQL.

The main ETL stages are:

**Extract**

* Load `support-tickets.csv` using Pandas.
* Convert date and timestamp fields into appropriate datetime formats.

**Transform**

* Standardize column names and text fields.
* Normalize categorical values.
* Convert columns to appropriate data types.
* Derive `resolution_min`.
* Derive `is_open`.
* Derive `has_csat`.
* Derive `opened_month`.
* Derive `frt_sla_flag`.
* Derive `resolution_sla_flag`.

**Validate**

* Check required columns.
* Check `ticket_id` for null and duplicate values.
* Validate categorical values.
* Validate date ordering.
* Apply the data quality rules defined during the data quality analysis.

**Load**

* Load the prepared ticket data into the PostgreSQL data warehouse.
* The warehouse is then used as the primary data source for SQL analysis and Power BI.

---

## 🗄️ PostgreSQL Data Warehouse

The cleaned ticket data is stored in **PostgreSQL** using a dimensional **star schema**.

The central fact table is `fact_tickets`, supported by four dimension tables:

```text
                         DIM_DATE
                            │
                            │
DIM_CHANNEL ───────► FACT_TICKETS ◄────── DIM_QUEUE
                            │
                            │
                      DIM_PRIORITY
```

### Warehouse Tables

| Table          | Purpose                                            |
| -------------- | -------------------------------------------------- |
| `fact_tickets` | Stores one record for each customer support ticket |
| `dim_date`     | Provides calendar attributes for date analysis     |
| `dim_channel`  | Defines available support channels                 |
| `dim_queue`    | Defines customer support queues                    |
| `dim_priority` | Defines ticket priority levels                     |

`fact_tickets` contains the main analytical measures and foreign keys connecting the ticket records to the dimension tables.

The `dim_date` table supports the ticket's opened and resolved dates through role-playing date relationships.

---

## SQL Business Analysis

SQL is used to analyze the PostgreSQL data warehouse and answer **18 defined business questions (BQ-01 to BQ-18)**.

The analysis covers:

* Total ticket volume
* Ticket subjects
* Queue volume
* Channel volume
* First Response Time by queue
* First Response Time by priority
* Resolution time by queue
* Resolution time by priority
* FRT vs. CSAT
* CSAT distribution and average
* Reopened tickets
* Reopened tickets by queue
* Open tickets by queue and priority
* Monthly ticket trends
* CSAT coverage and data completeness

The SQL analysis provides the analytical basis for the Power BI dashboard and business insight report.

---

## Power BI Dashboard

**Power BI connects directly to the PostgreSQL data warehouse** as the dashboard's primary data source.

```text
PostgreSQL
     │
     │ Direct Database Connection
     ▼
  Power BI
     │
     ├── KPI Cards
     ├── Ticket Volume Analysis
     ├── Queue & Channel Analysis
     ├── FRT Analysis
     ├── Resolution Analysis
     ├── CSAT Analysis
     ├── Reopened Ticket Analysis
     ├── Backlog Analysis
     └── Monthly Trends
```

This architecture allows the dashboard to use the structured warehouse tables rather than relying on the raw CSV file as the visualization source.

---

## Key Metrics

### Ticket Demand

* Total Tickets
* Tickets by Queue
* Tickets by Channel
* Tickets by Priority
* Monthly Ticket Volume
* Top Ticket Subjects

### Operational Performance

* First Response Time
* Average Resolution Time
* Resolution Time by Queue
* Resolution Time by Priority
* First Response Time by Queue
* First Response Time by Priority

### Customer Satisfaction

* Average CSAT
* CSAT Distribution
* CSAT by First Response Time Band
* CSAT Coverage

### Backlog & Reopened Tickets

* Total Open Tickets
* Open Tickets by Queue
* Open Tickets by Priority
* Reopened Ticket Count
* Reopened Tickets by Queue
* Reopen Rate

---

## Business Insights

The analysis identified several notable patterns:

* **Technical** is the largest queue, with **1,282 tickets (32%)**.
* **Email** is the largest channel, with **1,368 tickets (34%)**.
* **Technical** has an average resolution time of approximately **4,982 minutes**.
* **Technical** has the lowest average queue-level CSAT at **3.85**.
* There are **225 open tickets**, representing **5.63%** of all tickets.
* **Technical has 104 open tickets**, representing approximately **46.2%** of the open backlog.
* There are **285 reopened tickets**, representing **7.13%** of all tickets.
* **Technical has 84 reopened tickets**, the highest number among the queues.
* CSAT is available for **1,226 tickets (30.65%)**.

These are observed patterns in the dataset. The available data does not contain agent-level, staffing, customer contact-reason, or number-of-touch information, so specific operational causes cannot be confirmed from the dataset alone.

---

## Business Recommendations

1. **Investigate workload allocation for Technical and Integrations**
   Technical has the largest ticket volume and backlog, while Integrations has the longest response and resolution times.

2. **Consider an operational SLA target for Low-priority tickets**
   Low-priority tickets have an average resolution time of approximately 6,544 minutes. Any SLA target should be evaluated against actual operational requirements.

3. **Investigate reopened Technical tickets**
   Technical records 84 reopened tickets. Further analysis could examine common subjects and handling patterns associated with reopened tickets.

4. **Improve CSAT response coverage**
   CSAT is available for only 30.65% of tickets. Increasing survey completion would provide broader coverage for satisfaction analysis.

5. **Conduct follow-up analysis on reopened and multi-touch tickets**
   Additional customer interaction data could help determine whether reopened or multi-touch tickets are associated with lower CSAT.

---

##  Data Limitations

* CSAT coverage is **30.65%**, so CSAT findings are directional.
* Agent-level and staffing data are unavailable.
* Customer contact reasons and number of customer interactions are unavailable.
* The dataset represents a historical period rather than real-time operations.
* Observed relationships should not be interpreted as proven causal relationships.

---

##  Technology Stack

| Category                | Technology                 |
| ----------------------- | -------------------------- |
| Programming             | Python                     |
| Data Processing         | Pandas                     |
| Database                | PostgreSQL                 |
| Data Warehouse          | Star Schema                |
| Query Language          | SQL                        |
| Visualization           | Power BI                   |
| Exploratory Analysis    | Python / Pandas            |
| Documentation           | Markdown / Microsoft Word  |
| Development Environment | Jupyter Notebook / VS Code |

---

## 🚀 End-to-End Workflow

```text
Business Requirements
        ↓
Raw Customer Support CSV
        ↓
Python ETL & Data Quality Validation
        ↓
PostgreSQL Data Warehouse
        ↓
SQL Business Questions
        ↓
Power BI Dashboard
        ↓
Business Insights
        ↓
Recommendations
```

---

##  Project Context

This project was developed as a **Business Intelligence / Data Analytics portfolio project** using historical customer support ticket data.

The project demonstrates an end-to-end analytical workflow combining:

**Python ETL → PostgreSQL Data Warehouse → SQL → Power BI → Business Insights**

The objective is to transform raw operational ticket data into structured, decision-ready information for customer support and operations analysis.

---

##  License

This project is intended for portfolio and educational purposes.
