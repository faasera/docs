# Faasera AI Profiler — Hint Processing Modes

Faasera Profiler supports advanced techniques to detect, classify, and protect sensitive data in **unstructured text**.
You can configure the Profiler to use **AI-driven entity hinting**, **heuristic recognition**, or a **hybrid** of both
approaches.

This guide explains the available hint processing modes, their configuration, and how Faasera blends multiple techniques
like **dictionary matching**, **NLP models**, and **checksum validation** to enrich entity recognition.

---

## Hint Processing Modes

Faasera supports the following modes under the `aiModel.hintProcessingMode` setting:

| Mode                 | Description                                                              |
|----------------------|--------------------------------------------------------------------------|
| `AI_ONLY`            | Use only AI hint service results. Disables all heuristic detection.      |
| `HEURISTIC_ONLY`     | Use only heuristic recognizers (dictionary, NLP, checksum, etc.).        |
| `HYBRID` *(default)* | Combine both AI and heuristic results, merging them with disambiguation. |

---

## AI Hinting (FaaseraAI Hint Service)

When enabled, Faasera sends selected text values to an external Hinting Service (e.g., GLiNER) via HTTP POST. The
service returns structured **entity spans**:

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

These AI-detected spans are converted into internal `Span` objects with configurable mapping to `RecognizerType` via:

```json
"classificationToHintService": {
"FIRST_NAME": "first_name",
"EMAIL_ADDRESS": "email",
"SSN": "ssn"
}
```

The confidence value is preserved (rounded to 2 decimals) for sorting and disambiguation.

---

# What is Heuristic Recognition?

**Heuristic-based recognition** is a rules-based approach for identifying sensitive entities in text. Unlike AI models
that rely on statistical learning, heuristics use **predefined logic**, making them **fast**, **transparent**, and *
*highly customizable**. This approach is ideal for regulated environments that require deterministic behavior and
auditability.

Faasera Profiler implements several heuristic techniques:

---

### 1. NLP Models (Apache OpenNLP)

Faasera leverages **pre-trained NLP models** to detect structured entities in free-form text.

| Entity Type | Example Text              | Detected Entity |
|-------------|---------------------------|-----------------|
| Person Name | "My name is Alice Carter" | Alice Carter    |
| Location    | "I live in New York"      | New York        |

These models analyze linguistic patterns (tokens, POS tags) to infer the type of entity.

---

### 2. Dictionary-Based Matching

Faasera supports **custom term dictionaries** that define known sensitive keywords or entities.

**Example Dictionary:**

```json
{
  "COUNTRY_NAMES": [
    "Australia",
    "Canada",
    "Japan"
  ],
  "HEALTH_TERMS": [
    "asthma",
    "diabetes",
    "HIV"
  ]
}
```

**Example Input:**

```
"The patient was diagnosed with diabetes in Australia."
```

**Output:**

- `diabetes` → Health Term
- `Australia` → Country Name

Dictionaries are ideal for domain-specific use cases like healthcare, finance, or law.

---

### 3. Keyword-Based Recognition

You can define **keywords or token triggers** that imply sensitivity without requiring an entity match.

**Examples:**

- If the sentence contains `"credit card"` and a number → flag the number.
- If `"SSN"` is present → treat the next 9-digit pattern as sensitive.

This rule-based method helps catch context-sensitive values.

---

### 4. Checksum-Based Validation

Faasera supports **structural validation** using checksums and patterns. These techniques go beyond detection — they *
*verify** the integrity of the format.

| Type        | Example Input         | Detection Logic        |
|-------------|-----------------------|------------------------|
| Credit Card | `4111 1111 1111 1111` | Luhn Algorithm (valid) |
| National ID | `987654321`           | Pattern + checksum     |
| Tax Numbers | `TFN 123 456 782`     | Modulus-based checksum |

This ensures that detected entities are **valid**, not just pattern-matched.

---

### 5. Regex-Based Matching (Optional)

Advanced users may define **custom regular expressions** to capture specific formats.

**Example Regex:**

```regex
[A-Z]{3}-\d{4}
```

This pattern matches strings like `ABC-1234`. Useful for internal identifiers or industry-specific formats.

---

## Why Use Heuristics?

| Benefit              | Description                                                  |
|----------------------|--------------------------------------------------------------|
| **Speed**            | No external API calls or models required                     |
| **Predictability**   | Deterministic logic — same input yields same result          |
| **Auditability**     | Easy to explain and document logic for compliance            |
| ️**Customizability** | Users can create dictionaries, keywords, or regex rules      |
| **Fallback Ready**   | Works well as a baseline or fallback to AI-based recognition |

---

Heuristics form the **foundation of Faasera’s detection engine** and can be used independently or **combined with AI
hinting** to balance precision and performance across varied data environments.

## Workflow

```text
               ┌───────────────────────────────┐
               │  Raw Text Input from User     │
               └─────────────┬─────────────────┘
                             │
                   ┌─────────▼─────────────┐
                   │  Faasera Profiler     │
                   │  (Unstructured Mode)  │
                   └─────────┬─────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼───────┐   ┌────────▼─────────┐   ┌──────▼────────┐
│ AI Hint Mode  │   │ Heuristic Engine │   │ Config Engine │
│ (Model, etc..)│   │ NLP + Dictionary │   │ Policy Config │
└───────┬───────┘   └────────┬─────────┘   └──────┬────────┘
        │                    │                    │
        └────┬───────────────┴──────┬─────────────┘
             ▼                     ▼
         Hint Spans         Heuristic Spans
             │                     │
             └────┬────────┬───────┘
                  ▼        ▼
           Span Disambiguation Service
                  │
                  ▼
        Filter + Sort + Confidence Merge
                  │
                  ▼
        ┌─────────────────────────────┐
        │   Final Spans + Redaction   │
        └─────────────────────────────┘
```

---

## How to Configure

### In Your Policy JSON

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

### In faasera.config.properties file

Default (fallback) mode is HYBRID if not defined in JSON policy.

---

## Confidence & Overlap Handling

- Spans from AI and heuristic methods are assigned a `confidence` score (AI-provided or default).
- All spans are merged and passed to the **SpanDisambiguationService** to remove overlaps and deduplicate entities.
- Final `applied` spans are those with the highest confidence and no structural conflict.

---

## Use Case Scenarios

| Use Case                          | Recommended Mode     |
|-----------------------------------|----------------------|
| Domain-specific, AI-trained model | `AI_ONLY`            |
| Regulated data pipelines          | `HEURISTIC_ONLY`     |
| Enterprise general-purpose usage  | `HYBRID` *(default)* |

---

## Output Example (HYBRID)

Given input:

```text
"John Doe’s email is john@faasera.ai and SSN is 123-45-6789."
```

Output (combined spans):

```json
[
  {
    "text": "John",
    "label": "FIRST_NAME",
    "confidence": 0.76
  },
  {
    "text": "john@faasera.ai",
    "label": "EMAIL_ADDRESS",
    "confidence": 0.99
  },
  {
    "text": "123-45-6789",
    "label": "SSN",
    "confidence": 0.95
  }
]
```

---

## Summary

| Component               | Purpose                                            |
|-------------------------|----------------------------------------------------|
| `HintService`           | Sends text to AI service and fetches hint labels   |
| `HintMapper`            | Maps AI labels to internal RecognizerTypes         |
| `Unstructured Profiler` | Merges spans based on chosen mode and redacts text |

---

## Final Notes

- You can enable or disable any span generator (e.g., turn off regex or OpenNLP) via policy config.
- AI hinting gives a head start for dynamic or domain-specific data.
- HYBRID is best for broad use cases with real-world variation.

---

Need help fine-tuning your profiler pipeline? Reach out at [www.faasera.ai](https://www.faasera.ai) 
