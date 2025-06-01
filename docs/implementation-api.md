# Faasera API Integration Guide

This guide explains how to integrate with the Faasera platform using RESTful APIs for real-time data profiling, masking,
validation, and audit reporting.

---

## Overview

Faasera exposes REST APIs that allow programmatic access to its core services from any client (e.g., ETL jobs, apps,
scripts, serverless functions).

---

## Authentication

All endpoints require authentication via API Key or JWT (based on configuration).

**Example Header:**

```http
Authorization: Bearer <your_token_here>
```

---

## Profiling API

### Endpoint

```
POST /api/v1/profiler/run
```

### Payload (Sample)

```json
{
  "requestId": "abc123",
  "mode": "STRUCTURED",
  "columns": [
    { "name": "email", "value": "john.doe@example.com" },
    { "name": "dob", "value": "1985-02-03" }
  ]
}
```

### Response

```json
{
  "results": [
    { "column": "email", "label": "EMAIL_ADDRESS", "confidence": 0.98 },
    { "column": "dob", "label": "DOB", "confidence": 0.92 }
  ]
}
```

---

## Masking API

### Endpoint

```
POST /api/v1/mask/run
```

### Payload

```json
{
  "requestId": "mask-001",
  "mode": "MULTI_RULE_BATCH",
  "records": [
    { "ruleName": "EMAIL_ADDRESS", "value": "john.doe@example.com" },
    { "ruleName": "DOB", "value": "1985-02-03" }
  ]
}
```

### Response

```json
{
  "maskedRecords": [
    { "value": "j***.d**@example.com", "ruleName": "EMAIL_ADDRESS" },
    { "value": "1985-**-**", "ruleName": "DOB" }
  ]
}
```

---

## Validation API

### Endpoint

```
POST /api/v1/validate
```

### Payload

```json
{
  "original": "john.doe@example.com",
  "masked": "j***.d**@example.com",
  "rule": "EMAIL_ADDRESS"
}
```

### Response

```json
{ "valid": true, "issues": [] }
```

---

## Audit & Risk API

### Endpoint

```
GET /api/v1/audit/risk-summary?datasetId=<id>
```

### Response

```json
{
  "riskScore": 78,
  "highRiskFields": ["SSN", "CREDIT_CARD"],
  "complianceGap": ["HIPAA", "PCI-DSS"]
}
```

---

## File-based API (Optional)

Faasera supports base64 or presigned file uploads for batch profiling/masking:

```
POST /api/v1/file/profile
POST /api/v1/file/mask
```

---

## Response Schema

All responses follow a standard envelope:

```json
{
  "status": "SUCCESS",
  "data": { ... },
  "errors": []
}
```

---

## API Testing

Use tools like Postman or curl:

```bash
curl -X POST https://api.faasera.ai/api/v1/mask/run \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d @masking-payload.json
```

---

## Notes

- APIs are stateless and scalable.
- Rate limits and audit logs are enabled.
- For custom integrations (e.g., Airflow), use the SDK instead.

---

## Need Help?

For access credentials or support, contact: [support@faasera.ai](mailto:support@faasera.ai)