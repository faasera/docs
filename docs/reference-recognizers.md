# 📖 Recognizer Types

This document outlines all the built-in and custom recognizer types available in Faasera for entity classification,
profiling, and policy-based masking.

---

## 🔐 Recognizer Categories

Recognizers are grouped into functional categories to help simplify policy creation and reporting.

---

### 🧍 Personal Identifiable Information (PII)

| Recognizer                               | Description                             |
|------------------------------------------|-----------------------------------------|
| `age`                                    | Age values                              |
| `date-of-birth`                          | Date of birth                           |
| `first-name`                             | First name (seeded)                     |
| `last-name`                              | Last name (seeded)                      |
| `middle-name`                            | Middle name (seeded)                    |
| `firstname-lastname`                     | Combined first and last name            |
| `lastname-firstname`                     | Combined last and first name            |
| `gender`                                 | Gender (seeded)                         |
| `blood-group`                            | Blood group (seeded)                    |
| `nationality`                            | Nationality (seeded)                    |
| `ssn`                                    | Social Security Number                  |
| `tax-id`                                 | Tax ID (preserved)                      |
| `passport-number`                        | Passport Number                         |
| `drivers-license-number`                 | Driving License                         |
| `npi`                                    | National Provider Identifier            |
| `au-medicare`, `au-tfn`, `uk-nhid`, etc. | Region-specific identifiers (preserved) |
| `occupation`                             | Job title (seeded)                      |

---

### 💳 Financial Information

| Recognizer                                   | Description                       |
|----------------------------------------------|-----------------------------------|
| `account-number`                             | Bank account                      |
| `bank-routing-number`                        | Routing number                    |
| `creditcard-number`                          | Credit card number                |
| `creditcard-cvc`                             | Card security code                |
| `creditcard-expiry`                          | Card expiration date              |
| `iban-code`                                  | International Bank Account Number |
| `currency`, `currency-code`, `currency-name` | Currency types                    |
| `money`                                      | General currency values           |
| `pan`                                        | Primary account number            |

---

### 📍 Location & Address

| Recognizer                                       | Description        |
|--------------------------------------------------|--------------------|
| `city`, `state`, `county`, `region`, `country`   | Geo fields         |
| `street-address`, `street-name`, `street-number` | Address components |
| `zip-code`, `postcode`, `city-code`, etc.        | Postal metadata    |
| `latitude-longitude`                             | Coordinates        |

---

### 📞 Contact & Communication

| Recognizer                               | Description     |
|------------------------------------------|-----------------|
| `phone-number`, `phone-number-extension` | Phone metadata  |
| `email-address`                          | Email address   |
| `web-domain`, `url`, `username`          | Online metadata |

---

### Technology

| Recognizer                                           | Description            |
|------------------------------------------------------|------------------------|
| `ip-address`, `mac-address`                          | Network identifiers    |
| `guid`                                               | Unique identifiers     |
| `regex`, `vin`, `bitcoin-address`, `tracking-number` | Pattern-matched tokens |

---

### Healthcare Information

| Recognizer                                            | Description         |
|-------------------------------------------------------|---------------------|
| `diagnosis`, `treatment`, `prescription`              | Clinical content    |
| `medical-results`, `imaging-results`                  | Lab/imaging outputs |
| `clinical-notes`, `symptoms`                          | EHR entries         |
| `hospital`, `hospital-abbreviation`, `physician-name` | Provider data       |

---

### Temporal

| Recognizer                                        | Description                 |
|---------------------------------------------------|-----------------------------|
| `date`, `datetime`, `time`, `year`, `month`, etc. | Timestamp & duration fields |

---

### Extensible Recognizers

Faasera supports the following **dynamic recognizer types**, allowing users to define custom logic using dictionaries or
masking functions:

| Recognizer          | Description                                                                                                            |
|---------------------|------------------------------------------------------------------------------------------------------------------------|
| `dictionary-lookup` | Enables user-supplied dictionaries for custom entity matching. Useful for proprietary or domain-specific vocabularies. |
| `mask-functions`    | Enables UDM-based profiling using user-defined masking logic, tied directly to policy functions.                       |

> ⚠️ **Note**: These are not used in default policies. They are dynamically injected based on runtime metadata or
> advanced profiling rules.

---

For more technical details or to define your own recognizers, refer to
the [Policy Format Guide](./reference-policy-format.md).
