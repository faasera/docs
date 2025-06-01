# Masking Functions

Faasera provides a flexible set of masking functions tailored for different data types and regulatory needs. Functions
can be deterministic, randomized, or format-preserving.

## Common Masking Functions

| Function   | Description                                             |
|------------|---------------------------------------------------------|
| `SEED`     | Replace with random value from a dictionary seed list   |
| `PRESERVE` | Preserve format (e.g., date, phone) but scramble values |
| `HASH`     | Hash input using SHA-256 or configurable algorithm      |
| `NULLIFY`  | Replace with NULL                                       |
| `REDACT`   | Replace with constant or redacted characters            |
| `TRUNCATE` | Truncate to specified length                            |
| `FPE`      | Format-preserving encryption (with encryption config)   |

## Configuration Example

```json
"masking": {
  "EMAIL_ADDRESS": { "function": "REDACT", "params": { "replacement": "***@****.com" } },
  "SSN": { "function": "FPE", "params": { "key": "my-secret", "domain": "NUMERIC" } }
}
```

Each function supports optional parameters defined in the [Policy Format](./reference-policy-format.md).