# 🔄 Faasera Transformation Guide

The Faasera Transformation Engine provides flexible and policy-driven data transformations, enabling clean, consistent,
and anonymized datasets for AI/ML pipelines, data migration, and integration workflows. This guide walks through its key
capabilities, configuration, and usage patterns.

---

## Key Capabilities

| Capability                      | Description                                                                 |
|---------------------------------|-----------------------------------------------------------------------------|
| **Column-level transformation** | Apply functions like uppercase, trim, format, concat, regex replace.        |
| **Derived field generation**    | Generate new columns using expressions or lookup rules.                     |
| **Policy-driven mapping**       | Define reusable transformations for known source systems or schemas.        |
| **Conditional logic**           | Apply rules based on data values, column patterns, or data type heuristics. |
| **Integration-ready output**    | Output transformed data to any supported sink (e.g., RDBMS, Snowflake).     |

---

## When to Use Transformation

- Standardizing data formats across environments (e.g., `MM/DD/YYYY` to `YYYY-MM-DD`)
- Generating surrogate keys or synthetic identifiers before masking
- Preparing feature columns for analytics or ML models
- Cleaning messy or inconsistent data across rows and columns
- Implementing data harmonization for multi-source pipelines

---

## How It Works

```text
               +-----------------------+
               |  Source Data (Raw)    |
               +-----------------------+
                          |
                          ▼
                Faasera Transformation Engine
                          |
         +----------------+----------------+
         |                                 |
     Transform Policies                Lookup Tables
         |                                 |
         ▼                                 ▼
 +-----------------+              +------------------+
 | Column Mapping  |              | Derived Field Gen|
 | Regex / Format  |              | Conditional Rules|
 +-----------------+              +------------------+
                          |
                          ▼
               +------------------------+
               |  Transformed Output    |
               | (Masked or Cleaned)    |
               +------------------------+
```

---

## Configuring Transformations

Transformations are defined in the policy JSON under a `transformation` section.

### Example:

```json
"transformation": {
  "enabled": true,
  "rules": [
    {
      "column": "birthdate",
      "action": "FORMAT_DATE",
      "fromFormat": "MM/dd/yyyy",
      "toFormat": "yyyy-MM-dd"
    },
    {
      "column": "customer_name",
      "action": "UPPERCASE"
    },
    {
      "column": "location",
      "action": "REPLACE",
      "pattern": "NY",
      "replacement": "New York"
    }
  ]
}
```

---

## Supported Transformation Actions

| Action         | Description                                                 |
|----------------|-------------------------------------------------------------|
| `TRIM`         | Remove leading/trailing whitespace                          |
| `UPPERCASE`    | Convert string to uppercase                                 |
| `LOWERCASE`    | Convert string to lowercase                                 |
| `FORMAT_DATE`  | Change date format (requires `fromFormat`, `toFormat`)      |
| `REPLACE`      | Replace substrings using regex                              |
| `CONCAT`       | Concatenate multiple columns                                |
| `MASK_IF_NULL` | Apply masking only if column is null or empty               |
| `MAP_LOOKUP`   | Replace value using external lookup (e.g., JSON dictionary) |
| `CUSTOM_EXPR`  | Apply transformation using expression engine (advanced)     |

---

## Best Practices

- Use `FORMAT_DATE` before masking to ensure date consistency
- Combine `TRIM` + `UPPERCASE` to normalize text for entity matching
- Avoid `CUSTOM_EXPR` unless performance trade-offs are acceptable
- Chain transformations logically to match downstream schema requirements
- Reuse policies across environments via Faasera Orchestrator

---

## Integration with Other Modules

- **Masking Engine**: Transformations are applied **before** masking (unless explicitly configured otherwise)
- **Validation Engine**: Can compare original and transformed results for rule enforcement
- **Synthetic Generator**: Uses transformed output as seed/reference for generated data

---

## Output Destinations

Transformed data can be sent to:

- JDBC sinks (PostgreSQL, MySQL, Oracle, etc.)
- Cloud Data Warehouses (Snowflake, BigQuery)
- File formats (CSV, Parquet, JSON)
- Streaming systems via NiFi, Kafka Connect, etc.

---

## Example Use Case

You want to anonymize customer records by:

1. Formatting date of birth
2. Cleaning and uppercasing names
3. Replacing codes with friendly values

**Resulting Output:**

| Input Name | Input DOB  | Code | → | Output Name | Output DOB | Output Location |
|------------|------------|------|---|-------------|------------|-----------------|
| John Doe   | 12/31/1985 | NY   | → | JOHN DOE    | 1985-12-31 | New York        |

---

Need help building your transformation policies?

Contact the Faasera team at [www.faasera.ai](https://www.faasera.ai) for onboarding assistance.
