# Faasera User Guide: Synthetic Data Generation

Faasera’s Synthetic Data Engine enables the generation of realistic, lineage-aware datasets for non-production
environments. It supports multiple generation modes, schema preservation, referential integrity, and seeded randomness —
all aligned with compliance and privacy goals.

---

## Key Features

| Feature                        | Description                                                        |
|--------------------------------|--------------------------------------------------------------------|
| Schema-Based Generation        | Generates data based on existing schema or user-defined templates  |
| Seeded Randomness              | Ensures repeatability for test data generation using fixed seeds   |
| Referential Integrity          | Maintains relationships between foreign keys and primary keys      |
| Context-Aware Fields           | Applies domain logic (e.g., age vs. DOB, state vs. zip)            |
| Privacy-Enhanced Defaults      | Avoids re-identification by generating safe, non-real data         |
| AI-Augmented Value Suggestions | Optionally uses AI hints for plausible names, locations, or values |

---

## Configuration Modes

You can define generation rules per table and per column using a JSON policy structure. Key elements include:

```json
{
  "tableName": "customers",
  "generationType": "SYNTHETIC",
  "columnRuleMap": {
    "firstname": { "type": "SEED", "seedFileName": "firstnames.csv" },
    "lastname": { "type": "SEED", "seedFileName": "lastnames.csv" },
    "email": { "type": "PRESERVE", "pattern": "{firstname}.{lastname}@example.com" },
    "dob": { "type": "DATE_RANDOM", "range": { "start": "1980-01-01", "end": "2000-12-31" } }
  },
  "primaryKeyColumn": "customer_id",
  "compositeKeyColumn": "customer_id+dob"
}
```

---

## Generation Types

| Type          | Description                                                          |
|---------------|----------------------------------------------------------------------|
| `SEED`        | Picks value from a seeded list (e.g., names, cities)                 |
| `RANDOM`      | Random value with optional format (e.g., strings, numbers)           |
| `PRESERVE`    | Derived based on another field (e.g., email from firstname/lastname) |
| `DATE_RANDOM` | Generates random date within a specified range                       |
| `FPE_ENCODED` | Applies format-preserving encoding using FPE keys                    |

---

## Referential Integrity

To preserve key relationships:

- Define `primaryKeyColumn` and `compositeKeyColumn`
- Faasera ensures foreign keys reference valid generated records
- Applies across tables if run within a pipeline

---

## Using AI for Plausible Generation (Optional)

You can enrich values using external hinting or AI copilots (e.g., to generate more localized names, job titles, etc.)

- Define `useAIHinting: true` in your config
- AI will propose semantically realistic values for synthetic generation

---

## Output & Integration

- Output is written to the target database or file system (e.g., CSV, Parquet, JSON)
- Can be invoked via:
    - Faasera UI
    - REST API
    - Spark SDK
    - Cloud Functions

---

## Example Use Cases

| Use Case                   | Benefit                                        |
|----------------------------|------------------------------------------------|
| QA/Test Data Generation    | Enables safe, representative test environments |
| Pre-Sales Demos            | Creates realistic demo datasets                |
| Data Science Bootstrapping | Generates data for ML model experimentation    |
| Sandboxing New Features    | Safe testbed without real PII/PHI              |

---

## Compliance Notes

- All synthetic data is **non-reversible**
- Can be flagged in lineage as `synthetic`
- Seeded values should never originate from real PII files

---

## Need Help?

- Refer to your platform policy documentation for generation rules
- Reach out via [www.faasera.ai](https://www.faasera.ai) for consulting