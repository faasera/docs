
# 🔁 Faasera LLM Privacy Proxy – End-to-End Workflow

This document explains how the Faasera Proxy intercepts, redacts, and routes LLM (Large Language Model) requests securely using Faasera Profiler.

---

## 1. 📨 Client Sends Request

A user or app sends a standard OpenAI-style request to the proxy instead of directly to OpenAI or another LLM provider.

```http
POST http://localhost:8080/v1/chat/completions
Authorization: Bearer <API_KEY>
Content-Type: application/json

{
  "model": "gpt-4",
  "messages": [
    {"role": "user", "content": "What is Jane Doe's SSN 123-45-6789?"}
  ]
}
```

---

## 2. 🔒 Faasera Proxy Intercepts the Request

- The proxy extracts the `messages` array from the payload.
- It prepares to redact each message.

---

## 3. ✂️ Faasera Profiler Redacts Each Message

For each message:

```http
POST /api/profile-mask/default
Content-Type: application/json
{
  "value": "What is Jane Doe's SSN 123-45-6789?"
}
```

The Faasera Profiler returns:

```json
{
  "replacement": "What is [[NAME]]'s SSN [[SSN]]?"
}
```

---

## 4. 🔁 Proxy Rebuilds Redacted Payload

- Redacted content replaces the original `message.content`.
- Entire payload is now safe.

---

## 5. 🎯 Proxy Routes to Correct Target

Based on the environment config (`DEFAULT_TARGET=openai|claude|gemini|local`), the proxy determines where to send the request.

Example:
```bash
https://api.openai.com/v1/chat/completions
```

---

## 6. 🚀 Proxy Forwards Redacted Payload to LLM

- Uses client-supplied `Authorization` header.
- Sends cleaned messages to selected LLM endpoint.

---

## 7. 🧠 LLM Processes Request

Example LLM response:

```json
{
  "id": "chatcmpl-xyz",
  "choices": [{
    "message": {
      "role": "assistant",
      "content": "I'm sorry, but I cannot access personal information such as SSNs."
    }
  }]
}
```

---

## 8. 📤 Proxy Sends Response to Client

- Response is returned **as-is** from the LLM to the client.

---

## 🧭 Text-Based Diagram

```text
┌────────────────────────┐
│  1. Client Request     │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│  2. Proxy Receives     │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────────┐
│  3. Call Faasera Profiler. │
│  for Redaction             │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────┐
│  4. Replace Message    │
└────────────┬───────────┘
             │
             ▼
┌──────────────────────────────────────────┐
│  5. Route to LLM (OpenAI, Claude, etc.)  │
└────────────┬─────────────────────────────┘
             │
             ▼
┌────────────────────────┐
│  6. Send to LLM        │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│  7. Receive Response   │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│  8. Return to Client   │
└────────────────────────┘
```

---

## 🔐 Security Highlights

- **Redaction always occurs _before_ reaching any LLM.**
- **Supports HTTPS (TLS), tokens, and masking policies.**
- **Customizable backend and provider switching.**

---

## 🔗 Learn More

Visit [https://www.faasera.ai](https://www.faasera.ai) for additional SDKs, plugins, and compliance features.
