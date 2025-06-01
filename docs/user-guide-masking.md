
# Faasera Masking User Guide

This guide covers how to configure, execute, and optimize masking workflows in Faasera. The masking engine is designed for **deterministic**, **non-deterministic**, and **policy-driven** transformations across structured and semi-structured data.

---

## ✨ Key Features

- Deterministic & consistent masking (e.g., same input → same output)
- Format-preserving encryption (FPE)
- Realistic synthetic value generation
- Referential integrity preservation
- Custom masking per column or entity type

---

## 🔧 Masking Policy Structure

Masking in Faasera is controlled via a `Policy` JSON which includes:

```json
{
  "maskingRules": {
    "FIRST_NAME": {
      "type": "SEED",
      "seedFileName": "first-names.txt",
      "preserveFormat": true
    },
    "EMAIL_ADDRESS": {
      "type": "PRESERVE",
      "preserveDomain": true
    },
    "SSN": {
      "type": "FPE",
      "fpeKeyId": "default-fpe"
    }
  }
}
```

### Common Masking Types

| Type       | Description                                                        |
|------------|--------------------------------------------------------------------|
| `SEED`     | Uses a predefined list (e.g., names, cities)                       |
| `PRESERVE` | Keeps format or domain intact (e.g., email@domain.com)            |
| `REDACT`   | Replaces with a fixed or blank value                               |
| `FPE`      | Format-preserving encryption using configurable key                |
| `HASH`     | One-way hash using SHA-256 or user-defined algorithm               |

---

## 🧠 Deterministic vs Non-Deterministic

| Mode             | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| Deterministic    | Ensures the same input always maps to the same output.                   |
| Non-deterministic| Each run may produce different outputs (e.g., `REDACT`, synthetic values)|

---

## ⚙️ Configuration Options

Each rule can be enriched with:

- `preserveFormat`: Keeps structure (e.g., credit cards: 1234-5678-****-****)
- `maskKeyColumn`: Used to preserve referential integrity across tables
- `seedFileName` + `seedFieldPosition`: Custom seed data for consistent replacement
- `regexPreserveGroups`: When using regex masks, preserve matched groups

---

## 🧪 In-Place vs Source-to-Target Masking

| Mode               | When to Use                        | Example                    |
|--------------------|------------------------------------|----------------------------|
| In-Place           | Modify existing DB/table           | Production field masking   |
| Source-to-Target   | Copy + mask from source to target  | Dev/test DB environments   |

---

## 🔁 Multi-Table Workflows

Masking workflows can operate across multiple tables with shared policies, respecting:

- Column-specific rules
- Primary/foreign key constraints
- Composite identifiers for deterministic output

---

## 🔐 Security Considerations

- Masking rules can be tied to specific keys (e.g., FPE, HMAC)
- All masking logic is logged for traceability
- Option to enable preview-only (no writes) for dry runs

---

## 📥 Sample Input

```json
[
  {
    "first_name": "Alice",
    "email": "alice@example.com",
    "ssn": "123-45-6789"
  }
]
```

## 📤 Output (Masked)

```json
[
  {
    "first_name": "Grace",
    "email": "grace@masked.com",
    "ssn": "186-71-9983"
  }
]
```

---

## 🧩 Masking Rule Reference

| Field              | Supported Rule Types       |
|--------------------|----------------------------|
| `first_name`       | SEED, PRESERVE             |
| `last_name`        | SEED, HASH                 |
| `email`            | PRESERVE, REDACT           |
| `ssn`              | FPE, HASH, REDACT          |
| `credit_card`      | FPE, REGEX, REDACT         |

---

## 🧪 Testing Tips

- Use `"dryRun": true` to preview changes
- Set `"loggingLevel": "DEBUG"` to trace every span replacement
- Run validation pipeline post-masking to ensure referential integrity

---

For more details or advanced examples, visit [https://www.faasera.ai](https://www.faasera.ai)
