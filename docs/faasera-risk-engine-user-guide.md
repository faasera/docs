# Risk Engine & Audit Recommender — User Guide & Technical Specifications

This guide provides detailed instructions and technical specs for deploying and using the Faasera Risk Engine & Audit
Recommender, available as a container image via AWS Marketplace.

---

## Overview

Faasera’s Risk Engine automatically classifies datasets, calculates compliance risk scores, and suggests audit actions
based on the presence of PII, usage patterns, and access controls.

---

## What Does the Risk Engine Do?

- Detects sensitive fields (e.g., EMAIL, SSN, CREDIT_CARD)
- Assigns a risk score (0–100) per dataset or attribute
- Categorizes risk level (Low → Critical)
- Recommends audits based on real-world exposure and control gaps

---

## Risk Calculation

| Factor                  | Description                                                      |
|-------------------------|------------------------------------------------------------------|
| **PII Classification**  | Based on recognized entity types (e.g., EMAIL, SSN, CREDIT_CARD) |
| **Data Sensitivity**    | Mapped against internal classification or compliance standards   |
| **Volume & Frequency**  | Larger datasets or high-access frequency increase exposure       |
| **Environment Type**    | Dev/test environments with prod data are high-risk               |
| **Retention Policy**    | Long-lived data increases exposure                               |
| **Access Control Gaps** | RBAC/ACL absences amplify overall risk                           |

---

## Risk Level Categories

| Risk Range | Level       | Description                                                 |
|------------|-------------|-------------------------------------------------------------|
| 80–100     | Critical 🔴 | Immediate remediation recommended                           |
| 60–79      | High 🟠     | Requires strong controls, often linked to production PII    |
| 30–59      | Medium 🟡   | May contain sensitive data, monitor access and usage        |
| 0–29       | Low 🟢      | No sensitive exposure detected, minimal compliance concerns |

---

## Data Classification Mapping

Mapped to international standards:

- **GDPR**: Name, Email, IP Address, National ID
- **HIPAA**: Health conditions, Insurance info
- **PCI-DSS**: Credit card numbers
- **CCPA / PDPA**: Contact info, biometric identifiers

---

## Audit Recommender

| Scenario                               | Suggested Audit Action                      |
|----------------------------------------|---------------------------------------------|
| Sensitive data in non-prod environment | Add masking or synthetic data pipeline      |
| PII fields lack masking policy         | Apply default or custom masking functions   |
| Outdated or unmonitored datasets       | Trigger retention or deletion policy review |
| Unknown column types                   | Recommend profiling and manual review       |
| Broad external access detected         | Recommend RBAC hardening and access logging |

---

## Technical Specifications

| **Specification**       | **Details**                                                   |
|-------------------------|---------------------------------------------------------------|
| **Container Type**      | Docker-compatible Linux container                             |
| **Base Image**          | `eclipse-temurin:17-jdk-alpine`                               |
| **Architecture**        | `x86_64`                                                      |
| **CPU Requirements**    | Minimum 2 vCPU / Recommended 4 vCPU                           |
| **Memory Requirements** | Minimum 4 GB / Recommended 8 GB                               |
| **Disk Space**          | ~500 MB base image                                            |
| **Ports**               | `8080` (API server)                                           |
| **Input Format**        | JSON payloads via REST API                                    |
| **Output Format**       | JSON                                                          |
| **Authentication**      | JWT-based auth (verify via `Authorization: Bearer <token>`)   |
| **Supported Standards** | GDPR, HIPAA, PCI-DSS, CCPA, PDPA, NIST                        |
| **Scalability**         | Stateless; horizontally scalable                              |
| **Data Persistence**    | None (stateless)                                              |
| **Logging**             | JSON logs to stdout; compatible with CloudWatch               |
| **Startup Time**        | ~1–3 seconds                                                  |
| **Deployment Targets**  | ECS, EC2, Fargate, EKS, Docker Compose                        |
| **License**             | Proprietary Faasera License (with Apache 2.0, MIT components) |

---

## Environment Variables

These must be set as part of your deployment configuration (in ECS task definition, Fargate config, Kubernetes secrets,
or `.env` in Docker).

| **Variable**              | **Description**                                                 |
|---------------------------|-----------------------------------------------------------------|
| `FAASERA_LICENSE_TOKEN`   | Signed JWT license token from Faasera                           |
| `FAASERA_PLATFORM_TYPE`   | Platform identifier (e.g., `REST`)                              |
| `FAASERA_PUBLIC_KEY_PATH` | Path to public key used to validate license                     |
| `FAASERA_REGION`          | Logical region identifier (e.g., `us.east`, `ap.southeast`)     |
| `JWT_SIGNATURE_SECRET`    | Secret for verifying user JWT tokens                            |
| `JWT_TOKEN_EXPIRATION`    | Expiry duration of JWT in seconds (e.g., `3600`)                |
| `FE_RCA_USER`             | Optional basic user credential for local access (e.g., `admin`) |
| `FE_RCA_PASS`             | Password for the above user (e.g., `supersecret`)               |

---

# Functional Capabilities — Faasera Risk & Audit Engine

## Risk Scoring Capabilities

| **Endpoint**                              | **Description**                              |
|-------------------------------------------|----------------------------------------------|
| `POST /risk/summary/by-name`              | Summary risk score for list of PII tag names |
| `POST /risk/summary/by-code`              | Summary risk score for PII tag codes         |
| `POST /risk/details/by-name`              | Detailed risk scores by PII tag names        |
| `POST /risk/details/by-code`              | Detailed risk scores by PII tag codes        |
| `POST /risk/summary/standards/by-name`    | Compliance standard summary by name          |
| `POST /risk/summary/standards/by-code`    | Compliance standard summary by code          |
| `POST /risk/standards/percentage/by-name` | Standard-level % contribution                |
| `POST /risk/summary/breakdown/by-name`    | Summary with risk and % breakdown            |
| `POST /risk/summary/breakdown/by-code`    | Same as above for code                       |
| `POST /risk/standards/weighted-summary`   | Weighted compliance score by standard        |

## Audit Recommendations

| **Endpoint**                    | **Description**                               |
|---------------------------------|-----------------------------------------------|
| `POST /audit/recommend/by-name` | Returns audit recommendations by PII tag name |
| `POST /audit/recommend/by-code` | Returns audit recommendations by PII tag code |

## Compliance Standards

| **Endpoint**             | **Description**               |
|--------------------------|-------------------------------|
| `GET /standards`         | List all compliance standards |
| `POST /standards`        | Create new standard           |
| `GET /standards/{id}`    | Get standard by UUID          |
| `PUT /standards/{id}`    | Update standard               |
| `DELETE /standards/{id}` | Delete standard               |

## PII Recognizers

| **Endpoint**               | **Description**        |
|----------------------------|------------------------|
| `GET /recognizers`         | List all recognizers   |
| `GET /recognizers/{id}`    | Get recognizer by UUID |
| `POST /recognizers`        | Create recognizer      |
| `PUT /recognizers/{id}`    | Update recognizer      |
| `DELETE /recognizers/{id}` | Delete recognizer      |

## Standard Category Mappings

| **Endpoint**            | **Description**             |
|-------------------------|-----------------------------|
| `GET /mappings`         | Fetch simplified mappings   |
| `GET /mappings/full`    | Fetch full mapping entities |
| `GET /mappings/{id}`    | Get mapping by UUID         |
| `POST /mappings`        | Create mapping              |
| `PUT /mappings/{id}`    | Update mapping              |
| `DELETE /mappings/{id}` | Delete mapping              |

## Sample Request Format

```
POST /risk/summary/by-name
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

["FIRST_NAME", "EMAIL", "CC_NUMBER"]
```

### Response

```json
{
  "overallRiskScore": 83,
  "standardBreakdown": {
    "GDPR": 91,
    "HIPAA": 76,
    "PCI-DSS": 80
  },
  "tagCoverage": [
    {
      "tag": "EMAIL",
      "risk": 90
    },
    {
      "tag": "CC_NUMBER",
      "risk": 86
    }
  ]
}
```

## How It Works

- Tags or Codes are passed in the request (e.g., `EMAIL`, `NATIONAL_ID`, `cc-number`, etc.)
- These map to **recognizers** which contain metadata (risk level, data category, domain)
- Recognizers link to **compliance standards** (e.g., GDPR, HIPAA, PCI-DSS)
- The engine calculates **composite risk scores**, **weighted distributions**, and **audit recommendations**
- Responses are returned via REST in JSON format
- No state is persisted — ideal for ephemeral, API-driven deployments

## Authentication Flow

- All routes are protected by JWT
- Use a reverse proxy (e.g., AWS API Gateway, ALB) or custom auth layer for public access
- JWT is validated using the `JWT_SIGNATURE_SECRET`

## Licensing

Faasera Risk Engine is licensed under a proprietary commercial license.

- All deployments must include a valid `FAASERA_LICENSE_TOKEN` (JWT)
- Contact [support@faasera.ai](mailto:info@faasera.ai) for license requests

## Logging & Monitoring

- All logs output to **stdout** in **JSON** format
- Compatible with **Docker logs**, **AWS CloudWatch**, **Datadog**, and similar tools

## Prerequisites Checklist

- Valid JWT license token (`FAASERA_LICENSE_TOKEN`)
- Platform environment variables set (ECS/Fargate/etc.)
- Public key for JWT validation (`FAASERA_PUBLIC_KEY_PATH`)
- Runtime: Java 17+, Docker compatible
- Valid JSON input schema when calling endpoints

## Usage Instructions Guide

- [Amazon EKS Deployment Guide](./risk-eks-deployment.md)
- [AWS Fargate Deployment Guide](./docs/faasera-executive-summary.md)
- [Docker Deployment Guide](./docs/faasera-executive-summary.md)

