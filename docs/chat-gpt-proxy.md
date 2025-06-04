## 📚 Table of Contents

- [Why Use This Proxy?](#why-use-this-proxy)
- [Quickstart](#quickstart)
- [How It Works](#-how-it-works)
- [Example Request](#example-request)
- [Security](#security-notes)
- [Project Structure](#project-structure)

---

**Faasera Chat Compliance Proxy** is a secure, pluggable reverse proxy that redacts sensitive information (PII, PHI,
PCI, etc.) from chat completion requests **before** forwarding them to OpenAI or any LLM endpoint. It
uses [Faasera Profiler](https://www.faasera.ai) to perform inline masking for compliance and privacy protection.

---

## Why Use This Proxy?

- No sensitive data reaches OpenAI or Claude unfiltered.
- Replaces names, SSNs, credit cards, and other identifiers with redacted values.
- Seamlessly integrates with OpenAI's `/v1/chat/completions`.
- Works with Claude, Gemini, and local LLMs too.
- TLS-enabled and fully self-hosted — your data stays private.

---

## Features

- Redacts text using Faasera Profiler (`/api/profile-mask/<policyName>`).
- Supports multiple LLM targets: OpenAI, Claude, Gemini, local.
- Compatible with any OpenAI-compatible client (curl, SDKs, LangChain, etc.).
- TLS support using auto-generated or custom certs.
- Simple Python HTTP server (no external frameworks required).

---

## Quickstart

### 1. Clone the Repo

```bash
git clone https://github.com/faasera/faasera-openai-proxy.git
cd faasera-openai-proxy
```

### 2. Create a `.env` file

```env
# TLS configuration
FAASERA_PROXY_PORT=8080
FAASERA_CERT_FILE=certs/cert.pem
FAASERA_KEY_FILE=certs/key.pem

# Profiler
FAASERA_PROFILER_ENDPOINT=https://localhost:8000

# Target LLM
DEFAULT_TARGET=openai
OPENAI_API_KEY=sk-xxx
CLAUDE_API_KEY=sk-ant-xxx
```

---

### 3. Generate Certificates (Optional for HTTPS)

```bash
make cert
```

---

### 4. Run the Proxy

```bash
make run
```

This starts an HTTPS server on `https://localhost:8080`.

---

## Example Request

```bash
curl https://localhost:8080/v1/chat/completions \
  -k \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "My SSN is 123-45-6789 and my name is John Smith"}]
  }'
```

### What Happens:

1. Text is sent to Faasera Profiler.
2. Sensitive parts (e.g. `John Smith`, `123-45-6789`) are redacted.
3. Cleaned request is forwarded to OpenAI or the configured LLM.
4. Response is returned to the client.

---

## Multi-LLM Support

The proxy can forward redacted requests to any of these:

| Provider | Env Value | Path                   |
|----------|-----------|------------------------|
| OpenAI   | `openai`  | `/v1/chat/completions` |
| Claude   | `claude`  | `/v1/messages`         |
| Gemini   | `gemini`  | `/v1beta/models/...`   |
| Local    | `local`   | `/chat`                |

Change the target by setting:

```env
DEFAULT_TARGET=claude
```

---

## How It Works

- [High level workflow](./chat-proxy-workflow.md)

---

## Project Structure

```
faasera-openai-proxy/
├── main.py                       # Entry point (TLS-enabled server)
├── Makefile                      # Run and cert helpers
├── requirements.txt              # Minimal dependencies
├── config/
│   └── env.example               # Sample env vars
├── certs/
│   └── cert.pem, key.pem         # TLS certificates (auto-generated)
├── docs/
│   └── README.md                 # This file
├── scripts/
│   └── generate_cert.sh          # Self-signed cert generator
├── faasera_openai_proxy/
│   ├── __init__.py
│   ├── proxy.py                  # Core proxy logic (mask + forward)
│   └── targets.py                # LLM target router
└── tests/
    ├── __init__.py
    └── test_proxy.py             # Basic tests
```

---

## Security Notes

- Use valid TLS certs in production.
- Proxy never logs or stores sensitive content.
- Only redacted text is sent to LLM providers.

---

## License

MIT. Includes third-party dependencies under their respective licenses.

---

## About Faasera

> Faasera is a real-time data compliance platform that automates data profiling, masking, validation, and privacy
> controls across modern AI and data pipelines.

---

## 📞 Support & Resources

| Resource    | Link                                                                        |
|-------------|-----------------------------------------------------------------------------|
| 🌐 Website  | [faasera.ai](https://faasera.ai)                                            |
| 📧 Support  | [support.faasera.ai](https://support.faasera.ai)                            |
| 🎥 YouTube  | [youtube.com/@FaaseraAI](https://www.youtube.com/@FaaseraAI)                |
| 💼 LinkedIn | [linkedin.com/company/faasera](https://www.linkedin.com/company/faasera-ai) |
| 📘 Facebook | [facebook.com/faasera.ai](https://www.facebook.com/faaseraAi)               |

---
