# 🧩 Faasera Component Breakdown

This document provides a deeper look at each core component of the Faasera platform — including responsibilities,
inputs/outputs, and extension points — to help developers, architects, and platform teams understand how each part
contributes to the system.

---

## 🧠 Core Service Components

### 1. **Profiling Service**

- **Purpose:** Identify sensitive data (PII/PHI) using AI models, patterns, keywords, NLP heuristics, and checksum
  validation.
- **Inputs:** Raw data tables, file extracts, schema metadata
- **Outputs:** Detected fields with classification labels, confidence scores, and suggested masking functions
- **Supports:** AI hint enrichment (e.g., GLiNER/LLM), context-aware profiling, language-specific recognizers
- **Extensibility:** Add new recognizers, AI hint adapters, language models

---

### 2. **Masking Engine**

- **Purpose:** Apply masking to sensitive data using deterministic or non-deterministic functions
- **Inputs:** Policy file (JSON), data table, rule mappings
- **Outputs:** Masked datasets with referential integrity preserved
- **Supports:** In-place and source-to-target masking
- **Mask Types:** FPE, Hash, Redact, Preserve, Nullify, Seeded values
- **Extensibility:** Plug-in custom masking functions

---

### 3. **Transformation Service**

- **Purpose:** Perform inline transformations during masking or data generation
- **Functions:** Concat, substring, arithmetic, regex rewrite, normalization
- **Inputs:** Schema + transformation config
- **Outputs:** Transformed dataset
- **Use Cases:** Data reshaping, standardization, synthetic enrichment

---

### 4. **Synthetic Data Generator**

- **Purpose:** Generate realistic test data using lineage-aware and seeded models
- **Inputs:** Schema + policy + generation strategy
- **Outputs:** Fully synthetic datasets for dev/test/ML use
- **Modes:** Full generation, hybrid masking+generation, constraint-based seeding
- **Extensibility:** Custom seed files, grammar-based generators, conditional logic

---

### 5. **Validation Service**

- **Purpose:** Validate integrity and structure of masked/synthetic data
- **Checks:** Format preservation, schema alignment, referential consistency, anomaly detection
- **Outputs:** Validation report with success/failure metrics, error diffing
- **Used for:** Compliance audits, data quality checks, A/B masking validation

---

### 6. **Risk & Audit Engine**

- **Purpose:** Assign risk scores and suggest remediation based on profiling and masking results
- **Inputs:** Field classification, exposure scope, applied policy, retention strategy
- **Outputs:** Risk score per table/column, audit recommendations
- **Use Cases:** DPO/CISO dashboards, compliance reporting, SLA enforcement

---

### 7. **Faasera Copilot (Private GPTs)**

- **Purpose:** Enable conversational workflows for compliance discovery and assistance
- **Capabilities:**
    - Ask "Where is SSN stored?"
    - "Which columns are unmasked?"
    - "Map this policy to GDPR Article X"
- **Backed By:** Domain-adapted LLMs
- **Extensibility:** Custom instructions, fine-tuned models, tenant-specific datasets

---

## ⚙️ Orchestration & Execution

### 8. **Faasera Orchestrator**

- **Purpose:** Coordinate pipeline execution (profiling, masking, validation, etc.)
- **Features:**
    - Task-level retries and status tracking
    - Multi-table orchestration
    - Batch and parallel mode
- **Inputs:** Job config, connection metadata, policy references

---

### 9. **Execution Engines**

- **Modes:**
    - **Cloud Functions:** Real-time, stateless masking and profiling
    - **SDKs:** Embedded execution in Spark/Databricks jobs (Java, PySpark)
    - **UI Invocations:** Triggered via visual platform with logs and tracking
- **Security:** All engines support token-based access and offline licensing

---

## 🔌 Connectors & Integration

### 10. **Connector Layer**

- **Supported Systems:** PostgreSQL, Oracle, MySQL, MongoDB, Snowflake, Parquet, BigQuery, etc.
- **Pipeline Tools:** Apache NiFi, Azure Data Factory, Airflow, Control-M
- **Formats:** JDBC, REST, Avro, Parquet, JSON, Flat files
- **Extensibility:** Add new connectors using plugin interface

---

## 🔐 Shared Infrastructure

- **Audit Logs:** Available per pipeline and task
- **Encryption:** TLS in transit, AES/FPE at rest (configurable)
- **RBAC:** Role-based control via Faasera UI or external identity providers
- **Licensing:** Serverless functions + SDKs support offline token validation, version control

