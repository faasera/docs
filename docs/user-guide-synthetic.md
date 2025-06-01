# Faasera Synthetic Data Generation — User Guide

This guide explains how to use the Faasera Synthetic Data module to generate safe, realistic, and lineage-aware test data for development, analytics, and QA use cases.

---

## ✨ Overview

Faasera Synthetic Data Generator allows users to:

- Replace sensitive data with fake but realistic values.
- Preserve referential integrity across related tables.
- Generate synthetic data that mimics original statistical distribution.
- Use the same policy engine as masking, ensuring consistent treatment.

---

## 📦 Key Features

| Feature                      | Description                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| **Seed-Based Generation**    | Uses seed files for deterministic or constrained value sets.                |
| **Field-Aware Logic**        | Applies domain-specific generation (e.g., names, dates, credit cards).     |
| **Lineage Consistency**      | Ensures synthetic data is relationally consistent across joins.             |
| **Custom Rule Support**      | Define custom generators for business-specific entities.                    |

---

## 🧬 Supported Generator Types

| Generator Type     | Use Case Example                      | Notes                                      |
|--------------------|----------------------------------------|--------------------------------------------|
| `FAKE_NAME`        | First/Last names                       | Locale-aware                               |
| `FAKE_EMAIL`       | Email addresses                        | Optional domain control                    |
| `FAKE_DATE`        | Birthdates, registration dates         | With configurable range                    |
| `FAKE_CREDIT_CARD` | Dummy card numbers (Luhn-valid)        | Brand-specific supported                   |
| `SEED_BASED`       | From seed file with column constraints | Deterministic or random from list          |
| `CUSTOM_LOGIC`     | Regex or lookup-based                 | Requires plugin or lambda function support |

---

## 🛠️ Configuration Options

Synthetic data generation is governed by the **masking policy**, using `maskFunction.type = GENERATE`.

### Example Policy Snippet

```json
{
  "columnRules": {
    "email": {
      "maskFunction": {
        "type": "FAKE_EMAIL"
      }
    },
    "customer_type": {
      "maskFunction": {
        "type": "SEED_BASED",
        "seedFileName": "customer_type.csv",
        "seedSeparator": ",",
        "seedFieldPosition": 0
      }
    }
  }
}
```

### Runtime Hints (Optional)

You can control generation using:

- **Locale**: Affects names, cities, dates.
- **Null Probability**: Simulates real-world missing values.
- **Format Templates**: For phone, date, or ID number styles.

---

## 🧩 Integration Modes

| Mode              | Description                                         |
|-------------------|-----------------------------------------------------|
| **In-place**      | Replace original column values in source DB         |
| **Source → Target** | Populate a new database or table with synthetic data |
| **Standalone**    | Generate data samples without an original dataset   |

---

## 🎯 Usage Scenarios

| Scenario                             | Synthetic Approach           |
|--------------------------------------|------------------------------|
| Load testing with fake users         | FAKE_NAME + FAKE_EMAIL       |
| Sensitive system without masking     | GENERATE mode for all PII    |
| Replace all values with lineage      | SEED_BASED + Referential Map |
| Generate GDPR/CCPA-compliant datasets | CUSTOM_LOGIC + audit trail   |

---

## 🧪 Sample Output

Input Row:

```json
{
  "name": "John Smith",
  "email": "john@real.com",
  "dob": "1980-01-01"
}
```

After Generation:

```json
{
  "name": "Alice Williams",
  "email": "alice@testmail.io",
  "dob": "1994-09-23"
}
```

---

## 🛡️ Compliance Notes

- All synthetic values are **non-reversible** unless seed-based determinism is explicitly configured.
- Generation rules can be exported and versioned for audit purposes.
- Referential joins are maintained by `maskKeyColumn` + `compositeKeyColumn` mapping.

---

## 📘 Best Practices

- Use `SEED_BASED` generation to ensure test consistency.
- Avoid overfitting generated data to real records.
- Add `nullProbability` for realism.
- Combine `masking` and `generation` in hybrid environments.

---

For advanced setup or consulting, contact the Faasera team at [www.faasera.ai](https://www.faasera.ai)