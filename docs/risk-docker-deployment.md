# Faasera Risk & Audit Engine Deployment on Docker

This guide walks you through deploying the Faasera Risk & Audit Engine using Docker in a local or VM-based environment.

---

## 📦 Prerequisites

- Docker installed (version 20.10+)
- Linux or MacOS (recommended)
- Access to Faasera container image via Docker registry (contact info@faasera.ai)

---

## 🛠️ Step-by-Step Deployment

### 1. **Pull the Image**

```bash
docker pull faasera/risk-audit-engine:latest
```

> If hosted on private registry, authenticate first:

```bash
docker login registry.faasera.ai
docker pull registry.faasera.ai/faasera/risk-audit-engine:latest
```

---

### 2. **Run the Container**

```bash
docker run -d \
  --name faasera-engine \
  -p 8080:8080 \
  -e MICRONAUT_ENVIRONMENTS=docker \
  -e JWT_SECRET=your-secret-key \
  faasera/risk-audit-engine:latest
```

---

### 3. **Verify the Deployment**

- Open browser: [http://localhost:8080](http://localhost:8080)
- Use `Authorization: Bearer <your-token>` for secure endpoints.

---

## Example API Request

```bash
curl -X POST http://localhost:8080/risk/summary/by-name \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '["FIRST_NAME", "EMAIL_ADDRESS"]'
```

---

## Logs

- Logs are printed to `stdout`

```bash
docker logs -f faasera-engine
```

---

## Volume Mounting (Optional)

To persist logs or add config files:

```bash
docker run -d \
  -v /host/logs:/app/logs \
  -v /host/config:/app/config \
  faasera/risk-audit-engine:latest
```

---

## Health Check

```bash
curl http://localhost:8080/health
```

---

## License & Support

- License: Proprietary Faasera License
- Contact: support@faasera.ai
