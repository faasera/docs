
# 🛡️ Faasera Risk Engine & Audit Recommender — User Guide

This guide explains how the Faasera platform assesses sensitive data risk, assigns risk levels, and suggests audit improvements using its built-in Risk Engine and Audit Recommender.

---

## 📊 What Does the Risk Engine Do?

Faasera’s Risk Engine automatically classifies datasets based on sensitivity, privacy exposure, and usage context. It computes a **risk score** for each dataset, attribute, or workload to help you prioritize governance actions.

---

## 🧠 Risk Calculation

Each column or dataset is assigned a **risk score** based on:

| Factor                  | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **PII Classification**   | Based on recognized entity types (e.g., EMAIL, SSN, CREDIT_CARD).           |
| **Data Sensitivity**     | Mapped against internal classification or compliance standards.             |
| **Volume & Frequency**   | Larger datasets or high-access frequency increase exposure.                 |
| **Environment Type**     | Dev/test environments flagged with higher risk if production data is reused.|
| **Retention Policy**     | Stale or long-retained data increases long-term exposure.                   |
| **Access Control Gaps**  | Risk is amplified if RBAC/ACLs are not defined or enforced.                 |

Each dimension contributes to a composite risk score (0–100), where higher values indicate greater compliance risk.

---

## 🏷️ Risk Level Categories

| Risk Range | Level        | Description                                                  |
|------------|--------------|--------------------------------------------------------------|
| 80–100     | Critical 🔴   | Immediate remediation recommended                           |
| 60–79      | High 🟠        | Requires strong controls, often linked to production PII    |
| 30–59      | Medium 🟡      | May contain sensitive data, monitor access and usage        |
| 0–29       | Low 🟢         | No sensitive exposure detected, minimal compliance concerns |

---

## 🧩 Data Classification Mapping

Risk levels are assigned using internal recognizer types and mappings to compliance standards like:

- **GDPR** (e.g., Name, Email, IP Address)
- **HIPAA** (e.g., Health conditions, Insurance info)
- **PCI-DSS** (e.g., Credit card numbers)
- **CCPA / PDPA** (e.g., Contact info, biometric identifiers)

Each field is linked to compliance obligations and risk rulesets.

---

## 🧠 Audit Recommender

Faasera’s Audit Recommender provides **smart suggestions** based on detected risk patterns:

| Scenario                                               | Suggested Audit Action                              |
|--------------------------------------------------------|-----------------------------------------------------|
| Sensitive data in non-prod environment                 | Add masking or synthetic data pipeline              |
| PII fields lack masking policy                         | Apply default or custom masking functions           |
| Outdated or unmonitored datasets                       | Trigger retention or deletion policy review         |
| Fields with unknown data type or classification        | Recommend profiling and manual review               |
| Columns accessed by multiple external services         | Recommend RBAC hardening and access logs            |

These recommendations are visible in the UI or exported via JSON report.

---

## ⚙️ Output Schema Example

Sample output when risk evaluation is enabled:

```json
{
  "tableName": "customer_records",
  "environment": "dev",
  "riskScore": 92,
  "riskLevel": "CRITICAL",
  "highRiskColumns": [
    {
      "column": "email",
      "entityType": "EMAIL_ADDRESS",
      "compliance": ["GDPR", "CCPA"]
    },
    {
      "column": "credit_card",
      "entityType": "CREDIT_CARD",
      "compliance": ["PCI-DSS"]
    }
  ],
  "auditRecommendations": [
    "Apply masking to 'credit_card' in non-prod",
    "Consider synthetic data generation for testing"
  ]
}
```

---

## 🛠️ How to Enable Risk Analysis

Risk scoring is optional and can be toggled in the profiling policy JSON:

```json
"risk": {
  "enabled": true,
  "riskThresholds": {
    "CRITICAL": 80,
    "HIGH": 60,
    "MEDIUM": 30,
    "LOW": 0
  },
  "auditRecommendations": true
}
```

This allows dynamic adjustment of risk boundaries and controls the generation of audit suggestions.

---

## 📈 Reporting & Visualization

- Risk heatmaps across environments, tables, and fields
- Compliance distribution charts (e.g., % of data falling under GDPR)
- Top riskiest datasets or most flagged environments

All data is queryable via Faasera UI dashboards or API exports.

---

## ✅ Best Practices

- Run Risk Engine post-profiling and pre-masking to catch exposures
- Use risk scores to prioritize compliance tasks and audits
- Regularly export audit recommendations for review by DPO/CISO teams
- Integrate with CI/CD to prevent unsafe data exposure in dev/test

---

Need help configuring enterprise-level risk scoring? Contact us at [www.faasera.ai](https://www.faasera.ai)
