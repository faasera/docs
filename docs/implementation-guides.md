# Faasera Implementation Guides

This section provides detailed guidance on integrating Faasera with your environment using SDKs, APIs, or cloud-native
services.

---

## SDK Integration (Java / PySpark)

Faasera SDKs offer drop-in modules for Java and PySpark environments. These SDKs let you embed Faasera’s profiling,
masking, and validation engines directly into your Spark pipelines or Java applications.

### Key Features

- Built-in profiling and masking functions
- Support for structured and semi-structured data
- Works with Snowflake, Databricks, Apache Spark, Hadoop
- Deterministic and format-preserving masking options
- On-the-fly policy generation using metadata

**See full guide:** `implementation-sdk.md` _(coming soon)_

---

## API Integration

Faasera’s REST APIs provide granular access to each platform component, enabling integration with any system that can
send HTTP requests.

### Key Use Cases

- Trigger profiling or masking tasks
- Upload policies or schema definitions
- Fetch audit logs and reports
- Orchestrate pipelines via Faasera Orchestrator APIs

Authentication is handled via JWT tokens with fine-grained scope permissions.

**See full guide:** `implementation-api.md` _(coming soon)_

---

## Cloud Functions

Deploy Faasera as event-driven serverless functions using:

- AWS Lambda
- Azure Functions
- Google Cloud Functions

These functions can be triggered via API Gateway, message queues, or event buses.

### Example Use Cases

- Real-time masking on data ingestion
- Validate records in motion
- Apply profiling logic inside ETL streams

Faasera’s cloud-native footprint makes it ideal for embedding compliance logic with zero operational overhead.

**See full guide:** `implementation-cloud.md` _(coming soon)_