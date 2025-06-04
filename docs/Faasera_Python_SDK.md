# Faasera Python SDK

The **Faasera Python SDK** provides async programmatic access to Faasera’s core services:

- **Profiler**: Masking, profiling, alerting
- **Risk Engine**: Compliance standards, risk scoring, audit suggestions
- **Hint Engine**: Entity detection via AI/NLP hints

---

## Quickstart

### 1. Install

```bash
pip install faasera-sdk
```

### 2. Set Environment Variables

```bash
export PROFILER_PASSWORD=admin
export RISK_PASSWORD=admin
```

---

##  Example Usage

```python
import asyncio
from faasera_sdk.client import FaaseraClient
from auth.simple_token_provider import SimpleLoginTokenProvider

async def get_client():
    profiler_token = SimpleLoginTokenProvider("http://localhost:8000", "admin", "PROFILER_PASSWORD")
    risk_token = SimpleLoginTokenProvider("http://localhost:9090", "admin", "RISK_PASSWORD")

    return FaaseraClient(
        profiler_url="http://localhost:8000",
        risk_url="http://localhost:9090",
        hint_url="http://localhost:9000",
        profiler_token=profiler_token,
        risk_token=risk_token
    )
```

---

##  Profiler APIs

```python
await faasera.profiler.mask("default", "EMAIL_ADDRESS", "john@example.com")
await faasera.profiler.mask_batch("default", "EMAIL_ADDRESS", ["john@example.com", "jane@example.com"])
await faasera.profiler.profile_batch("default", ["SSN 123-45-6789"])
await faasera.profiler.profile_and_mask("default", "Jane Smith", column_name="name")
await faasera.profiler.explain("My email is jane@example.com")
await faasera.profiler.get_policies()
await faasera.profiler.get_policy("default")
await faasera.profiler.get_alerts()
```

---

## Risk APIs

```python
await faasera.risk.get_risk_summary_by_name(["EMAIL_ADDRESS"])
await faasera.risk.get_risk_details_by_code(["SSN"])
await faasera.risk.get_breakdown_by_name(["EMAIL_ADDRESS"])
await faasera.risk.get_weighted_risk_by_standards(["EMAIL_ADDRESS"])
await faasera.risk.get_audit_recommendations_by_code(["EMAIL_ADDRESS"])
await faasera.risk.get_all_compliance_standards()
await faasera.risk.get_all_recognizers()
await faasera.risk.get_all_mappings()
```

---

## Hint APIs

```python
await faasera.hint.status()
await faasera.hint.find_entities("Barack Obama lives in Washington", labels=["name", "location"])
await faasera.hint.find_entities_batch(["Alice in Paris", "Bob in Berlin"], labels=["location"])
await faasera.hint.get_faasera_hints(["Jack Smith in Toronto"], labels=["name", "location"])
```

---

## Run All

```bash
python3 sdk_example.py
```

> Make sure to set environment variables and have Faasera Profiler, Risk, and Hint services running locally.

---

## Requirements

- Python 3.8+
- `httpx`, `asyncio`, `dotenv` (included in `requirements.txt`)

---

## 🔗 Learn More

- 🌐 Website: [https://faasera.ai](https://faasera.ai)
- 📘 Docs: [https://docs.faasera.ai](https://docs.faasera.ai)
- 📧 Support: [support@faasera.ai](mailto:support@faasera.ai)