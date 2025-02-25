# Google Pubsub Receiver

| Status                   |                       |
| ------------------------ |-----------------------|
| Stability                | [beta]                |
| Supported pipeline types | traces, logs, metrics |
| Distributions            | none                  |

> ⚠️ This is a community-provided module. It has been developed and extensively tested at Collibra, but it is not officially supported by GCP.
 
This receiver gets OTLP messages from a Google Cloud [Pubsub](https://cloud.google.com/pubsub) subscription.

The following configuration options are supported:

* `project` (Optional): The Google Cloud Project of the client connects to.
* `subscription` (Required): The subscription name to receive OTLP data from. The subscription name  should be a 
  fully qualified resource name (eg: `projects/otel-project/subscriptions/otlp`).
* `encoding` (Optional): The encoding that will be used to received data from the subscription. This can either be
  `otlp_proto_trace`, `otlp_proto_metric`, `otlp_proto_log`, or `raw_text` (see `encoding`).  This will only be used as 
  a fallback, when no `content-type` attribute is present.
* `compression` (Optional): The compression that will be used on received data from the subscription. When set it can 
  only be `gzip`. This will only be used as a fallback, when no `content-encoding` attribute is present.

```yaml
receivers:
  googlecloudpubsub:
    project: otel-project
    subscription: projects/otel-project/subscriptions/otlp-logs
    encoding: raw_json
```

## Encoding

You should not need to set the encoding of the subscription as the receiver will try to discover the type of the data
by looking at the `ce-type` and `content-type` attributes of the message. Only when those attributes are not set 
must the `encoding` field in the configuration be set. 

| ce-type] | ce-datacontenttype | encoding | description |
| --- | --- | --- | --- |
| org.opentelemetry.otlp.traces.v1 | application/protobuf |  | Decode OTLP trace message |
| org.opentelemetry.otlp.metrics.v1 | application/protobuf |  | Decode OTLP metric message |
| org.opentelemetry.otlp.logs.v1 | application/json |  | Decode OTLP log message |
| - | - | otlp_proto_trace | Decode OTLP trace message |
| - | - | otlp_proto_metric | Decode OTLP trace message |
| - | - | otlp_proto_log | Decode OTLP trace message |
| - | - | raw_text | Wrap in an OTLP log message |

When the `encoding` configuration is set, the attributes on the message are ignored.

The receiver can be used for ingesting arbitrary text message on a Pubsub subscription and wrap them in OTLP Log
message, making it a convenient way to ingest log lines from Pubsub.

## Pubsub subscription

The Google Cloud [Pubsub](https://cloud.google.com/pubsub) receiver doesn't automatically create subscriptions, 
it expects the subscription to be created upfront. Security wise it's best to give the collector its own 
service account and give the subscription `Pub/Sub Subscriber` permission.

The subscription should also be of delivery type `Pull`.

### Filtering

When the messages on the subscription are accompanied by the correct attributes and you only need a specific
type in your pipeline, the messages can be [filtered](https://cloud.google.com/pubsub/docs/filtering) on the 
subscription saving on egress fees.

An example of filtering on trace message only: 
```
attributes.ce-type = "org.opentelemetry.otlp.traces.v1"
AND
attributes.content-type = "application/protobuf"
```

[beta]: https://github.com/open-telemetry/opentelemetry-collector#beta
