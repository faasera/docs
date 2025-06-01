# Cloud Functions Integration Guide

Faasera supports seamless deployment of its core components via cloud-native Functions-as-a-Service (FaaS) on AWS,
Azure, and Google Cloud. This guide covers deploying Faasera Profiler and Masking services using cloud functions.

---

## Supported Platforms

| Cloud Platform | Function Type   | Use Cases                                      |
|----------------|-----------------|------------------------------------------------|
| AWS            | Lambda          | Real-time masking/profiling via API Gateway    |
| Azure          | Azure Functions | Batch & stream data compliance workflows       |
| Google Cloud   | Cloud Functions | Lightweight API-based masking for GCS/BigQuery |

---

## General Setup

All Faasera cloud functions follow the same deployment pattern:

1. Package your Faasera JAR or Python function.
2. Define your cloud-specific entry point.
3. Deploy via CLI or UI.
4. Connect triggers (HTTP, storage events, etc.).

---

## Package Structure

For Java-based functions:

```
/faasera-function/
├── build/libs/faasera.jar
├── config/
│   └── policy.json
└── handler/
    └── FunctionHandler.java
```

For Python:

```
/faasera-function/
├── main.py
├── requirements.txt
└── config/
    └── policy.json
```

---

## AWS Lambda

### Deployment Steps

1. **Build ZIP** with dependencies.
2. Create IAM Role with Lambda + VPC access (optional).
3. Deploy using:

```bash
aws lambda create-function   --function-name FaaseraFunction   --runtime java11   --handler com.faasera.FunctionHandler   --zip-file fileb://faasera.zip   --role arn:aws:iam::123456789:role/lambda-role
```

4. **Optional:** Attach API Gateway or EventBridge for triggers.

---

## Azure Functions

### Supported Types

- HTTP-triggered
- Timer-triggered (for scheduled profiling/masking)
- Blob-triggered (data lake file masking)

### Deployment

```bash
func azure functionapp publish faasera-comply-function
```

> Ensure you set the `FUNCTIONS_WORKER_RUNTIME` to `java` or `python` as per your implementation.

---

## Google Cloud Functions

### Deployment

```bash
gcloud functions deploy faasera-function   --runtime java11   --trigger-http   --entry-point com.faasera.FunctionHandler   --memory 512MB
```

---

## Configuration Options

Pass configurations using:

- Environment variables
- Encrypted files (with KMS)
- Secrets Manager (AWS) or Key Vault (Azure)

---

## Testing

All functions support:

- REST API testing using Postman/curl
- Event-driven triggers with test data
- Cloud logs via CloudWatch, Application Insights, or GCP Logs

---

## Example Payload (Masking)

```json
{
  "requestId": "xyz-123",
  "mode": "MULTI_RULE_BATCH",
  "records": [
    {
      "ruleName": "EMAIL_ADDRESS",
      "value": "john.doe@example.com"
    }
  ]
}
```

---

## References

- AWS Lambda: https://docs.aws.amazon.com/lambda
- Azure Functions: https://docs.microsoft.com/en-us/azure/azure-functions/
- Google Cloud Functions: https://cloud.google.com/functions