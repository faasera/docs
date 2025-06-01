
# 🧰 SDK Integration Guide (Java / PySpark)

This guide provides instructions to integrate the Faasera SDK within your data pipelines using either Java or PySpark environments.

---

## 🚀 Overview

The Faasera SDK enables you to embed core capabilities like profiling, masking, and validation directly into your ETL or ML workflows using Java or PySpark. This is ideal for high-throughput, low-latency scenarios where data privacy must be enforced in real-time.

---

## 📦 Supported Environments

| Environment | Integration Mode   | SDK Format       |
|-------------|--------------------|------------------|
| Java        | JAR / Maven        | faasera-core.jar |
| PySpark     | pip / `.whl`       | faasera_sdk.whl  |
| Databricks  | Notebook / Wheel   | `%pip install`   |

---

## 🔧 Java SDK Integration

### Step 1: Add the SDK

If using Maven:

```xml
<dependency>
    <groupId>ai.faasera</groupId>
    <artifactId>faasera-core</artifactId>
    <version>1.0.0</version>
</dependency>
```

Or download the JAR:

```shell
wget https://repo.faasera.ai/releases/faasera-core-1.0.0.jar
```

### Step 2: Initialize SDK

```java
FaaseraMaskingSDK sdk = new FaaseraMaskingSDK();
sdk.initialize("path/to/policy.json");
Dataset<Row> maskedDF = sdk.mask(inputDF);
```

### Step 3: Use in Spark Job

```java
inputDF.createOrReplaceTempView("input_table");
spark.udf().register("mask_udf", sdk.getUDF());
spark.sql("SELECT mask_udf(column1) FROM input_table").show();
```

---

## 🔥 PySpark SDK Integration

### Step 1: Install the Wheel

```python
%pip install faasera_sdk-1.0.0-py3-none-any.whl
```

### Step 2: Import and Initialize

```python
from faasera_sdk import FaaseraProfiler

profiler = FaaseraProfiler(policy_path="policy.json")
profiled_df = profiler.profile(spark_df)
```

### Step 3: Apply Masking

```python
from faasera_sdk import FaaseraMasker

masker = FaaseraMasker(policy_path="policy.json")
masked_df = masker.mask(spark_df)
```

---

## 🧪 Testing Locally

You can run the SDK with sample CSVs or Parquet files:

```bash
java -jar faasera-core.jar --mode=mask --input=./data.csv --output=./masked.csv
```

Or use PySpark CLI:

```bash
pyspark --packages faasera_sdk
```

---

## ✅ Tips & Best Practices

- Reuse policy files across stages (profile → mask → validate)
- Use broadcast joins to optimize masking at scale
- Disable hinting in air-gapped environments (set `"enabled": false` in policy)
- Use deterministic keys for consistent masking across sessions

---

## 📄 License Activation

For SDK-based deployments, a license token (offline or cloud-linked) is required. Configure the license path as:

```properties
faasera.license.path=/etc/faasera/license.jwt
```

Or set via environment:

```bash
export FAASERA_LICENSE_PATH=/etc/faasera/license.jwt
```

---

Need help? Visit [faasera.ai/docs](https://www.faasera.ai/docs) or reach out to support@faasera.ai.
