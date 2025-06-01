# Faasera Profiler Policy Format Guide

This document describes the structure and components of a Faasera **Policy Document**, which governs how profiling,
masking, and classification are performed across structured and un-structured datasets.

---

## Overview

Faasera supports dynamic and declarative **policy definitions** using JSON. Policies can be used to:

- Profile sensitive data using built-in or custom recognizers
- Apply masking rules and functions
- Define exclusions and context-aware logic
- Integrate AI-powered hint services (like GLiNER)
- Manage encryption and masking configurations

---

## Policy Structure

```json
{
  "name": "default-policy",
  "config": {
    ...
  },
  "recognizers": {
    ...
  },
  "exclusion": {
    ...
  },
  "exclusionPatterns": [
    ...
  ],
  "crypto": {
    ...
  },
  "fpe": {
    ...
  },
  "graphical": {
    ...
  }
}
```

---

## Recognizers

The `recognizers` section defines what types of data to detect.

```json
"recognizers": {
"FIRSTNAME": true,
"EMAIL_ADDRESS": true,
"CREDITCARD_NUMBER": true
}
```

You can use internal types or enrich with AI-based hinting (`classificationToHintService`) for third-party models like
GLiNER:

```json
"classificationToHintService": {
"name": "FIRSTNAME",
"email": "EMAIL_ADDRESS"
}
```

---

## Masking Rules

Defined in the `maskConfig` under `config`.

```json
"config": {
"maskConfig": {
"FIRSTNAME": {
"type": "SEED",
"seedFileName": "firstnames.txt",
"seedSeparator": ","
},
"EMAIL_ADDRESS": {
"type": "PRESERVE",
"preserveStructure": true
}
}
}
```

Supported types include:

- `SEED`
- `PRESERVE`
- `HASH`
- `REDACT`
- `RANDOM`
- `FPE` (format-preserving encryption)

---

## Exclusion

```json
"exclusion": {
"global": ["test", "sample"],
"fileBased": {
"emails.csv": ["noreply@example.com"]
}
}
```

You can also use `exclusionPatterns`:

```json
"exclusionPatterns": ["\d{4}-\d{4}"]
```

---

## Crypto

```json
"crypto": {
"algorithm": "AES",
"key": "base64-encoded-key=="
}
```

Optional `fpe` config for format-preserving encryption:

```json
"fpe": {
"radix": 10,
"key": "hex-key",
"tweak": "tweak-key"
}
```

---

## AI Hint Mapping

Use `classificationToHintService` in your policy when enriching labels from third-party models like GLiNER.

```json
"classificationToHintService": {
"name": "FIRST_AND_LASTNAME",
"ssn": "SSN"
}
```

ℹ️ **Note:** Third-party models may be under separate licenses (e.g., GLiNER from HuggingFace). Refer to respective
licenses for usage rights.

---

## Format & Usage

Faasera supports both JSON and YAML formats.

- Use **JSON** for API integration
- Use **YAML** for human-readable editing
- Policy is stored as JSON in the internal metadata database

---

## Sample Minimal Policy

```json
{
  "name": "sample-policy",
  "recognizers": {
    "FIRSTNAME": true,
    "SSN": true
  },
  "config": {
    "maskConfig": {
      "FIRSTNAME": {
        "type": "SEED",
        "seedFileName": "firstnames.txt"
      },
      "SSN": {
        "type": "REDACT"
      }
    }
  }
}
```

---

## 📌 Notes

- `dictionary-lookup` and `mask-functions` are reserved for **custom** UDM usage and are **not** used in profiling
  policies directly.
- Make sure to test all custom policies before applying in production pipelines.
- You can upload and manage policy files via the Faasera UI or API.

---

For further examples, please visit [faasera.ai](https://faasera.ai) or contact support.

