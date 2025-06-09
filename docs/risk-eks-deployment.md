# Faasera Risk & Audit Engine — EKS Deployment Guide

## Goal

To deploy the Faasera Risk & Audit Engine on **Amazon Elastic Kubernetes Service (EKS)** using best practices for
configuration, scaling, networking, and security.

---

## Prerequisites

- An AWS account with access to EKS and IAM privileges
- EKS cluster
- kubectl and `aws` CLI installed and configured
- Helm 3 installed
- Docker image of Faasera Engine published in ECR (or public registry)
- A valid JWT public key mounted as a Kubernetes secret (or injected via config)
- Optional: API Gateway, ALB Ingress Controller, CloudWatch logging

---

## EKS Deployment Architecture Overview

```
                 [Client / UI / API Gateway]
                            |
                     [AWS ALB Ingress]
                            |
                ┌─────────────────────────┐
                │       EKS Cluster       │
                │                         │
                │   ┌─────────────────┐   │
                │   │  Risk API Pod   │   │
                │   ├─────────────────┤   │
                │   │  Audit API Pod  │   │
                │   └─────────────────┘   │
                │        (Stateless)      │
                └─────────────────────────┘
                            |
                    [CloudWatch Logs]
```

---

## Step 1: Create Kubernetes Namespace

```bash
kubectl create namespace faasera
```

---

## Step 2: Define Deployment YAML (e.g. `faasera-deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: faasera-risk-audit
  namespace: faasera
spec:
  replicas: 3
  selector:
    matchLabels:
      app: faasera-risk-audit
  template:
    metadata:
      labels:
        app: faasera-risk-audit
    spec:
      containers:
        - name: faasera-container
          image: <YOUR_ECR_OR_DOCKER_IMAGE>
          ports:
            - containerPort: 8080
          env:
            - name: MICRONAUT_ENVIRONMENTS
              value: "eks"
            - name: PUBLIC_KEY_PATH
              value: "/keys/public.pem"
          volumeMounts:
            - name: jwt-public-key
              mountPath: /keys
              readOnly: true
      volumes:
        - name: jwt-public-key
          secret:
            secretName: faasera-public-key
```

---

## Step 3: Create JWT Secret

```bash
kubectl create secret generic faasera-public-key \
  --from-file=public.pem=./public.pem \
  --namespace=faasera
```

---

## Step 4: Expose Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: faasera-risk-audit-svc
  namespace: faasera
spec:
  selector:
    app: faasera-risk-audit
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```

---

## Step 5: Ingress Configuration (optional with ALB)

If you're using ALB Ingress Controller:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: faasera-ingress
  namespace: faasera
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/backend-protocol: HTTP
spec:
  rules:
    - http:
        paths:
          - path: /risk
            pathType: Prefix
            backend:
              service:
                name: faasera-risk-audit-svc
                port:
                  number: 80
```

---

## Step 6: Enable CloudWatch Logging (optional)

To forward logs to CloudWatch, configure your EKS worker nodes with a FluentBit/CloudWatch Agent DaemonSet.

---

## Step 7: Validate

```bash
kubectl get pods -n faasera
kubectl logs <pod-name> -n faasera
```

Then test using:

```bash
curl -X POST \
  http://<your-loadbalancer-url>/risk/summary/by-name \
  -H "Authorization: Bearer <your_jwt_token>" \
  -H "Content-Type: application/json" \
  -d '["email", "credit_card_number"]'
```
