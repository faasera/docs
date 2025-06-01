# 📖 Policy Format

Policies in Faasera are defined in JSON format. A policy defines how profiling, masking, and validation should behave for a given dataset.

## Example Policy Structure

```json
{
  "name": "default-policy",
  "recognizers": {
    "enabled": ["FIRST_NAME", "EMAIL_ADDRESS", "SSN"]
  },
  "masking": {
    "SSN": {
      "function": "FPE",
      "params": {
        "key": "encryption-key",
        "domain": "NUMERIC"
      }
    }
  },
  "aiModel": {
    "enabled": true,
    "hintProcessingMode": "HYBRID",
    "classificationToHintService": {
      "FIRST_NAME": "first_name",
      "EMAIL_ADDRESS": "email"
    }
  }
}
```

## Top-Level Keys

| Key         | Description                                  |
|-------------|----------------------------------------------|
| `name`      | Name of the policy                           |
| `recognizers` | Enabled recognizer types                   |
| `masking`   | Masking configuration per entity             |
| `aiModel`   | AI hinting and classification mapping        |