# Error Codes & Logs

This guide covers common Faasera runtime error codes and their recommended resolutions.

## Error Code Table

| Code   | Description                                   | Resolution                        |
|--------|-----------------------------------------------|-----------------------------------|
| `F001` | Connection to data source failed              | Check connection string / secrets |
| `F002` | Invalid policy format                         | Validate JSON schema              |
| `F003` | Unrecognized recognizer label                 | Check recognizer name in policy   |
| `F004` | Masking function not supported                | Verify function name / spelling   |
| `F005` | Data type mismatch in transformation pipeline | Verify column types and mappings  |
| `F999` | Internal service error                        | Contact Faasera support           |

## Log Location

Logs are generated in JSON format per execution and stored under:

```
/var/log/faasera/<execution_id>.log
```

Log levels supported: `INFO`, `WARN`, `ERROR`, `DEBUG`.

Logs are also streamable via API or accessible through the Faasera UI under the execution history page.