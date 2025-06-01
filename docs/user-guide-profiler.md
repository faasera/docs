# 🧪 Faasera Profiler Guide

Faasera Profiler supports advanced techniques to detect, classify, and protect sensitive data in **structured and unstructured** text. You can configure the Profiler to use **AI-driven entity hinting**, **heuristic recognition**, or a **hybrid** of both approaches.

This guide explains the available modes, how to configure them, and how Faasera blends techniques like **dictionary matching**, **NLP models**, and **checksum validation** to enrich entity recognition.

---

## 🔄 Hint Processing Modes

| Mode | Description |
|------|-------------|
| `AI_ONLY` | Use only AI hint service results. Disables all heuristic detection. |
| `HEURISTIC_ONLY` | Use only heuristic recognizers (dictionary, NLP, checksum, etc.). |
| `HYBRID` *(default)* | Combine both AI and heuristic results, merging them with disambiguation. |

---

## 🤖 AI Hinting (FaaseraAI Hint Service)

When enabled, Faasera sends selected text values to an external Hinting Service (e.g., GLiNER) via HTTP POST. The service returns structured **entity spans** like this:

```json
[
  {
    "start": 0,
    "end": 5,
    "text": "John",
    "label": "first_name",
    "score": 0.7638
  }
]
```

These spans are mapped to internal recognizer types:

```json
"classificationToHintService": {
  "FIRST_NAME": "first_name",
  "EMAIL_ADDRESS": "email",
  "SSN": "ssn"
}
```

---

## 🔍 Heuristic Recognition Techniques

### 1. NLP Models (Apache OpenNLP)

| Entity Type | Example |
|-------------|---------|
| Person Name | "My name is Alice Carter" → Alice Carter |
| Location    | "I live in New York" → New York |

---

### 2. Dictionary-Based Matching

Custom domain dictionaries:

```json
{
  "COUNTRY_NAMES": ["Australia", "Canada"],
  "HEALTH_TERMS": ["asthma", "diabetes"]
}
```

Input: “The patient was diagnosed with diabetes in Australia.”  
Output: `diabetes` → Health Term, `Australia` → Country Name

---

### 3. Keyword-Based Recognition

Trigger context keywords like:

- `"credit card"` followed by a number
- `"SSN"` followed by 9-digit pattern

---

### 4. Checksum-Based Validation

| Type | Example | Method |
|------|---------|--------|
| Credit Card | `4111 1111 1111 1111` | Luhn Algorithm |
| TFN | `123 456 782` | Modulus check |

---

### 5. Regex-Based Matching (Optional)

Define advanced expressions like:

```regex
[A-Z]{3}-\d{4}
```

---

## ⚙️ Why Use Heuristics?

| Benefit | Description |
|---------|-------------|
| Speed | No external API calls |
| Predictability | Same input → same result |
| Auditability | Easy to trace |
| Customizability | User-defined logic |
| Fallback | Works even if AI model fails |

---

## 🔁 Detection Workflow

```text
               Raw Text Input
                      ↓
              Faasera Profiler
        ┌──────────┬───────────┬───────────┐
        │  AI Hint │ Heuristics│ Config    │
        └────┬─────┴────┬──────┴────┬──────┘
             ↓          ↓           ↓
     Hint Spans  Heuristic Spans  Rules
             ↓          ↓           ↓
        Span Disambiguation
             ↓
   Filter → Sort → Merge Confidence
             ↓
       Final Spans + Redaction
```

---

## 🧾 Configuration

### In Policy JSON

```json
"aiModel": {
  "enabled": true,
  "hintProcessingMode": "HYBRID",
  "classificationToHintService": {
    "FIRST_NAME": "first_name",
    "EMAIL_ADDRESS": "email",
    "SSN": "ssn"
  }
}
```

---

## 📈 Confidence & Span Handling

- Each span has a `confidence` score (from AI or default).
- Merged via `SpanDisambiguationService` to remove overlaps.
- Highest-confidence span is selected.

---

## 🧠 Use Case Scenarios

| Use Case | Recommended Mode |
|----------|------------------|
| AI-trained model use | `AI_ONLY` |
| Regulated pipelines | `HEURISTIC_ONLY` |
| General-purpose | `HYBRID` |

---

## 🧪 Output Example

Input:  
```text
"John Doe’s email is john@faasera.ai and SSN is 123-45-6789."
```

Output:  
```json
[
  { "text": "John", "label": "FIRST_NAME", "confidence": 0.76 },
  { "text": "john@faasera.ai", "label": "EMAIL_ADDRESS", "confidence": 0.99 },
  { "text": "123-45-6789", "label": "SSN", "confidence": 0.95 }
]
```

---

## 🧩 Summary of Components

| Component | Purpose |
|-----------|---------|
| HintService | Sends text to AI |
| HintMapper | Maps labels to internal recognizers |
| Profiler | Combines and redacts spans |

---

## ✅ Final Notes

- HYBRID is default and best for mixed datasets
- You can disable AI or heuristic engines in policy
- Contact [www.faasera.ai](https://www.faasera.ai) for custom profilers
