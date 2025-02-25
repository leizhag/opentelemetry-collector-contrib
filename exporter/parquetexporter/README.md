# Parquet File Exporter

| Status                   |                       |
| ------------------------ |-----------------------|
| Stability                | [in-development]      |
| Supported pipeline types | traces, logs, metrics |
| Distributions            | none                  |

Sends pipeline data to Parquet files.

> :construction: This receiver is in beta and configuration fields are subject to change.

## Configuration

The following configuration options are required:

- `path` (no default): Export Parquet file path.

The following configuration options can also be configured:

TODO

Example:

```yaml
exporters:
  parquet:
    path: /var/output/log.parquet
```

The full list of settings exposed for this exporter is going to be documented later
with detailed sample configurations [here](testdata/config.yaml).

[in-development]:https://github.com/open-telemetry/opentelemetry-collector#in-development
