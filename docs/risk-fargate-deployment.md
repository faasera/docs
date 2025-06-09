# Faasera Risk & Audit Engine — AWS Fargate Deployment Guide

This guide walks you through deploying the Faasera Risk & Audit Engine using **AWS Fargate**, a serverless compute
engine for containers within Amazon ECS. This setup is ideal for running stateless, scalable microservices like Faasera
without managing underlying EC2 instances.

---

## 📦 Prerequisites

Before deploying to Fargate, ensure the following are ready:

- **AWS CLI v2** installed and configured (`aws configure`)
- **Docker** (to build the container image)
- **Amazon ECS Cluster** (Fargate-compatible)
- **Amazon ECR** (Elastic Container Registry)
- **IAM Role** with appropriate ECS and CloudWatch permissions
- **JWT Token Generator** or API Gateway for external access

---

## Step-by-Step Deployment

### 1. Build & Push Docker Image

```bash
# Authenticate Docker to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account_id>.dkr.ecr.us-east-1.amazonaws.com

# Build the image
docker build -t faasera-risk-audit .

# Tag the image
docker tag faasera-risk-audit:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/faasera-risk-audit:latest

# Push to ECR
docker push <account_id>.dkr.ecr.us-east-1.amazonaws.com/faasera-risk-audit:latest
```

---

### 2. Create ECS Task Definition

Use this Fargate-compatible example:

```json
{
  "family": "faasera-risk-task",
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "cpu": "1024",
  "memory": "2048",
  "networkMode": "awsvpc",
  "executionRoleArn": "arn:aws:iam::<account_id>:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "name": "faasera-risk",
      "image": "709825985650.dkr.ecr.us-east-1.amazonaws.com/faasera/faasera-risk-engine:latest",
      "portMappings": [
        {
          "containerPort": 8080,
          "hostPort": 8080,
          "protocol": "tcp"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/faasera-risk",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ]
}
```

Register it via:

```bash
aws ecs register-task-definition --cli-input-json file://faasera-task-def.json
```

---

### 3. Run the Task

Create or use an existing ECS cluster and run the service:

```bash
aws ecs create-service   --cluster faasera-cluster   --service-name faasera-risk-service   --task-definition faasera-risk-task   --launch-type FARGATE   --desired-count 1   --network-configuration 'awsvpcConfiguration={subnets=["subnet-abc123"],securityGroups=["sg-abc123"],assignPublicIp="ENABLED"}'
```

---

## Security Considerations

- Secure with **API Gateway + Cognito/JWT** for auth.
- Use **VPC private subnets** and **HTTPS ALBs**.
- Enable **CloudWatch Logs** for audit trails.

---

## Monitoring

- **CloudWatch Logs**: JSON logs captured for each API request
- **ECS Events**: Check for scaling failures or restarts
- **ALB Health Checks**: Configure on `/health` endpoint (if implemented)

---

## Validation

Test once deployed:

```bash
curl -X POST https://<your-alb-endpoint>/risk/summary/by-name   -H "Authorization: Bearer <jwt>"   -H "Content-Type: application/json"   -d '["EMAIL_ADDRESS", "PHONE_NUMBER"]'
```

---

## License

This deployment uses Faasera’s proprietary risk and audit engine under a commercial license. Contact **info@faasera.ai**
for license keys or evaluation access.