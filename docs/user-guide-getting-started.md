# Getting Started with Faasera

Welcome to Faasera! This guide will walk you through the quickest way to get started with Faasera — whether you're a
developer, data engineer, or compliance lead.

---

## What You Can Do with Faasera

- Profile structured or semi-structured data to detect sensitive fields
- Apply deterministic and non-deterministic masking
- Generate synthetic datasets for development, testing, or ML
- Run risk scoring and validate data integrity
- Use the Faasera Copilot for policy questions, data mapping, and more

---

## Installation Options

Choose one of the following:

### Option 1: Use the Hosted UI (Recommended)

No setup required. Contact [support@faasera.ai](mailto:support@faasera.ai) for a trial account.

### Option 2: Local Docker Environment

```bash
git clone https://github.com/faasera/faasera-platform.git
cd faasera-platform
docker-compose up -d
```

Access the UI at: [http://localhost:8080](http://localhost:8080)

### Option 3: Use SDK or Cloud Function

See:

- [Deployment Guide](./faasera-deployment-guide.md)
- [SDK Integration Guide](./implementation-sdk.md)

---

## Prerequisites

| Tool                         | Required For              |
|------------------------------|---------------------------|
| Docker / Docker Compose      | Local UI platform         |
| Java 11+ or PySpark          | SDK-based masking         |
| Postgres / MySQL / Snowflake | Test connection setup     |
| AWS CLI or Azure CLI         | Cloud function deployment |

---

## Directory Structure

```plaintext
faasera-platform/
├── api/                 # REST backend (Java/Micronaut or Spring Boot)
├── ui/                  # React-based frontend
├── config/              # Default policy examples and templates
├── sdk/                 # Java / PySpark SDK
├── examples/            # Sample pipelines and scripts
└── docker-compose.yml   # Local orchestration
```

---

## First Workflow Example (UI Mode)

1. Login to Faasera UI
2. Create a new Project → Environment → Connection
3. Upload a dataset or connect to a database
4. Run Profiler → Review identified fields
5. Define masking policy → Run masking job
6. Download or preview masked output
7. View risk dashboard or audit trail

---

## First Workflow Example (SDK Mode)

```java
FaaseraEngine engine = FaaseraEngine.builder()
    .withPolicy("masking-policy.yaml")
    .withInput("jdbc:postgresql://...")
    .withOutput("masked_output.csv")
    .build();

engine.runMasking();
```

---

## Next Steps

- 👉 [Run Your First Profiling Task](./user-guide-profiler.md)
- 🔒 [Apply Masking to Sensitive Fields](./user-guide-masking.md)
- 🤖 [Use Faasera Copilot](./user-guide-ui.md)

---

## Need Help?

- Documentation: [https://faasera.ai/docs](https://faasera.ai/docs)
- Email: [support@faasera.ai](mailto:support@faasera.ai)

