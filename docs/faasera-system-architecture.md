# Faasera System Architecture (Updated & Synced)

This document provides a high-level view of the Faasera architecture, explaining how core components work together to
deliver real-time data compliance across different environments.

---

## Core Design Principles

Faasera is designed to be:

- **Modular** – Each capability (profiling, masking, generation, validation) is loosely coupled and composable.
- **Serverless-first** – Deployed as Functions-as-a-Service (FaaS) where possible for scalability and efficiency.
- **Multi-modal** – Runs via SDKs, APIs, Cloud Functions, or UI without dependency on a specific environment.
- **Real-time & Streaming Ready** – Built to support batch, micro-batch, and streaming data workloads.
- **AI-native** – Integrates AI hinting, LLM copilots, and pattern-based classification to automate privacy decisions.

---

## High-Level Architecture

```plaintext
                        +----------------------------+
                        |      Faasera UI Platform    |
                        | (Dashboards, Copilot, APIs) |
                        +-------------+--------------+
                                      |
                                REST API / SDK
                                      |
              +-----------------------------------------------------+
              |                      Core Services                  |
              |                                                     |
       +------+-----+    +---------+---------+    +--------+--------+
       | Profiling   |    |  Masking Engine   |    | Validation       |
       | Service     |    | (Deterministic &  |    | Service          |
       +------+-----+    |  Randomized)       |    +--------+--------+
              |          +---------+---------+             |
              |                    |                       |
       +------+-----+    +--------+--------+    +--------+--------+
       | Transformation|    | Synthetic Data  |    | Risk & Audit     |
       | Service       |    | Generator       |    | Engine           |
       +---------------+    +-----------------+    +------------------+
                                      |
                        +-------------+-------------+
                        |     Faasera Orchestrator    |
                        |  (Pipelines, Tasks, UI)     |
                        +------+------+--------------+
                               |      |
     +-------------------------+      +--------------------------+
     |                                                        |
+----+------+                                         +--------+------+
| Cloud     |                                         | SDKs /        |
| Functions |                                         | Libraries     |
| (Lambda,  |                                         | (Java, Spark) |
| Azure)    |                                         +---------------+
+-----------+                                             
                               |
           +------------------+------------------------+
           |                   |                        |
    +------+-----+     +-------+-------+       +--------+-------+
    | Data Sources|     |  ETL Pipelines|       | Data Warehouses|
    | (RDBMS, etc)|     | (NiFi, ADF)   |       | (Snowflake, etc)|
    +------------+     +---------------+       +------------------+
```

---

## Component Layers

### 1. **Application Layer**

- Faasera UI (React + REST APIs)
- Used by end-users for job control, policy management, analytics, and risk insights
- Copilot Interface: LLM-powered assistant for compliance questions, data discovery, and policy mapping

### 2. **Service Layer**

- **Profiling Service**: AI + heuristic-based entity recognition
- **Masking Engine**: Applies deterministic or randomized masking per policy
- **Validation Service**: Compares original vs. masked data for integrity and anomaly detection
- **Transformation Service**: Supports field normalization, derivation, and concatenation
- **Synthetic Data Generator**: Generates realistic, lineage-aware test data
- **Risk & Audit Engine**: Scores datasets, identifies exposure risk, and suggests remediation actions

### 3. **Orchestration Layer**

- Central orchestrator for managing pipeline runs, task flow, and job metadata
- Handles retry, parallelism, and progress tracking
- Can be invoked independently or integrated with SDKs and serverless functions

### 4. **Execution Layer**

- Cloud-native functions for quick invocation (AWS Lambda, Azure Functions)
- SDKs for Spark, Databricks, Python/Java pipelines

### 5. **Connector Layer**

- Connectors for RDBMS, NoSQL, Parquet, SaaS apps, and streaming sources
- Plugin support for NiFi, Azure Data Factory, Airflow

---

## Deployment Topologies

| Environment | Supported Mode                 | Example                                  |
|-------------|--------------------------------|------------------------------------------|
| AWS         | Lambda + API Gateway           | Real-time masking via event triggers     |
| Azure       | Azure Functions + Blob/Synapse | Profiling & masking pipelines            |
| On-prem     | SDK + Command-Line             | Embedded into ETL batch jobs             |
| Databricks  | PySpark SDK + Notebook         | Inline masking for ML pipelines          |
| Spark       | Java SDK via UDFs              | Deterministic masking in transformations |

---

## Built-In Capabilities

| Capability             | Description                                                                                         |
|------------------------|-----------------------------------------------------------------------------------------------------|
| AI Profiler            | Combines NLP, Regex, Checksums with LLM hinting                                                     |
| Masking Engine         | Deterministic, FPE, Redact, Hash, Preserve, Seeded                                                  |
| Synthetic Data         | Lineage-aware generation of realistic records                                                       |
| Validation Engine      | Structural + semantic post-mask validation                                                          |
| Risk & Audit Engine    | Scores risk by column/table and generates audit recommendations                                     |
| Transformation Service | Field derivation, concatenation, normalization rules                                                |
| Copilot (Private GPTs) | AI assistant for compliance tasks like PII search, regulation mapping, and workflow recommendations |

---

## Security & Compliance

- All data in motion and at rest is encrypted (TLS + configurable key management)
- Supports audit logs, role-based access control (RBAC), and key rotation
- License-aware functions with offline token validation for SDKs

---

## Extensibility

Faasera can be extended with:

- Custom connectors (via plugin API)
- Custom masking rules or regex packs
- AI hint adapters (e.g., external NER APIs like GLiNER)
- Extend Copilot with domain-specific LLMs or regulatory Q&A
- Integration with workflow engines (e.g., Control-M, Step Functions)

---

## Faasera Copilot (Private GPTs)

Faasera includes a built-in AI assistant (Copilot) that helps non-technical users interact with the platform through
natural language. Use cases include:

- Exploring which datasets contain specific types of PII
- Mapping policies to GDPR, HIPAA, PCI-DSS controls
- Receiving masking function recommendations
- Asking compliance-related questions using conversational UI

Copilot runs on domain-adapted LLMs and can be extended with customer-specific regulatory language.
