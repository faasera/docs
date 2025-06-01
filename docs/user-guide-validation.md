# ✅ User Guide: Faasera Validation Service

The Faasera Validation Service ensures the accuracy, integrity, and structure of masked or synthetic data by comparing it against the original dataset or validation rules. It supports both **rule-based** and **structural validation**, helping teams verify that transformations have not compromised data utility or format compliance.

---

## 🔍 Why Use Validation?

| Benefit                  | Description                                                             |
|--------------------------|-------------------------------------------------------------------------|
| Data Integrity Checks    | Ensure key attributes retain relational integrity (e.g., FK/PK mappings)|
| Schema Conformance       | Detects schema mismatches between source and masked data               |
| Format Validation        | Confirms the output matches required formats (e.g., SSN, DOB)          |
| Quality Assurance        | Flags anomalies or inconsistencies introduced during masking            |
| Audit Traceability       | Outputs validation logs for compliance and traceability                 |

---

## ⚙️ Modes of Validation

Faasera supports the following validation modes:

| Mode               | Description                                                           |
|--------------------|-----------------------------------------------------------------------|
| `STRUCTURAL`       | Validates column formats, nullability, regex patterns, and data types |
| `SEMANTIC`         | Compares masked vs original value behavior or distribution            |
| `REFERENTIAL`      | Verifies foreign key and primary key relationships post-transformation|
| `POLICY_BASED`     | Enforces explicit rules defined in your masking or data policy files  |

---

## 🛠️ Configuration Example (Validation Policy)

Validation rules can be declared inline or in a separate validation policy:

```json
{
  "validation": {
    "enabled": true,
    "rules": [
      {
        "type": "STRUCTURAL",
        "column": "email",
        "pattern": "^[\w.-]+@[\w.-]+\.\w{2,}$"
      },
      {
        "type": "REFERENTIAL",
        "keyType": "FK",
        "columns": ["user_id"],
        "referencedTable": "users",
        "referencedColumns": ["id"]
      }
    ]
  }
}
```

---

## 📝 Example Output

After running validation, Faasera generates detailed reports:

```json
{
  "table": "customers",
  "status": "PASSED",
  "validationSummary": {
    "totalRows": 10000,
    "failedRows": 23,
    "errorTypes": {
      "regexMismatch": 10,
      "nullViolation": 5,
      "fkMismatch": 8
    }
  }
}
```

These reports are stored in the Faasera UI and can be exported to PDF/CSV or accessed via API.

---

## 🧠 Best Practices

- Use **STRUCTURAL** validation for format-sensitive fields like IDs, emails, and dates.
- Use **REFERENTIAL** validation after masking datasets with foreign key dependencies.
- Integrate validation into CI/CD or data QA pipelines for continuous assurance.
- Combine with **Audit Logs** for full traceability and compliance readiness.

---

## 🧩 Integration Options

| Environment    | Integration Example                                              |
|----------------|------------------------------------------------------------------|
| Airflow        | Validation task after masking DAG step                           |
| Azure Data Factory | Add validation activity in a pipeline before publishing      |
| Spark          | Run validation SDK step after masking                           |
| Snowflake      | Use UDFs or scripting to call Faasera SDK                        |

---

## ✅ Summary

The Validation Service ensures that post-masked or synthetic datasets retain structural, semantic, and relational integrity. It prevents compliance regressions and ensures that data quality is preserved after transformations — without requiring manual QA.

For more details, visit [www.faasera.ai](https://www.faasera.ai)