### Fluentbit exporter

This repo contains the fluentbit exporter for opentelemetry. It allows users to export the telemetry data collected by the various SDK's using a fluentbit/fluentd instance, using the fluentforward protocol.

Provided that you have a fluent instance running on the node, you can export spans to the collector (with the fluentforwardreceiver enabed) or any other fluent instance that is listening using the fluentforward protocol by installing this exporter in your application by simply using the `InstallNewPipeline()` method as shown:

```
hostport := "localhost:24224"
serviveName := "fluentforward"
retryTimeout := 10*time.Second
err := fluentforward.InstallNewPipeline(hostport, serviceName, retryTimeout)
```

While initializing the exporter, we can specify the timeout parameter, which will reconnect to the remote instance every `timeout` duration, which decreases the chances of writes failing due to connection issues.

If a write fails, the exporter will attempt to reconnect to the fluent instance and will retry the write again before returning.

The data mapping can be found below:

For a span:

| Otel-go representation   | Otel type          | Fluentbit representation   | Fluentbit type            |
| ------------------------ | -----------        | -------------------------- | ----------------          |
| TraceID                  | trace.ID           | TraceID                    | string                    |
| SpanID                   | trace.SpanID       | SpanID                     | string                    |
| ParentSpanID             | trace.SpanID       | ParentSpanID               | string                    |
| Name                     | string             | Name                       | string                    |
| SpanKind                 | trace.SpanKind     | SpanKind                   | int                       |
| StartTime                | time.Time          | StartTime                  | int64                     |
| EndTime                  | time.Time          | EndTime                    | int64                     |
| Attributes               | []label.KeyValue   | Attrs                      | map[label.Key]interface{} |
| DroppedAttributesCount   | int                | DroppedAttributeCount      | int                       |
| MessageEvents            | trace.Event        | MessageEvents              | []event                   |
| DroppedEventsCount       | int                | DroppedEventsCount         | int                       |
| Links                    | []trace.Link       | Links                      | []link                    |
| DroppedLinksCount        | int                | DroppedLinksCount          | int                       |
| StatusCode               | codes.Code         | StatusCode                 | string                    |
| StatusMessage            | string             | StatusMessage              | string                    |
| Resource                 | *resource.Resource | Resource                   | string                    |
| InstrumentationLibrary   | Instrumentation.Library | InstrumentationLibraryName, Version | string |


 Note: The field TraceState is missing in the go implementation

The mappings for an event and a link are as shown:

#### Link


| Otel-go representation   | Otel type        | Fluentbit representation   | Fluentbit type            |
| ------------------------ | -----------      | -------------------------- | ----------------          |
| TraceID                  | trace.ID         | TraceID                    | string                    |
| SpanID                   | trace.SpanID     | SpanID                     | string                    |
| Attributes               | []label.KeyValue | Attrs                      | map[label.Key]interface{} |


#### Event


| Otel-go representation   | Otel type        | Fluentbit representation   | Fluentbit type            |
| ------------------------ | -----------      | -------------------------- | ----------------          |
| Name                     | string           | Name                       | string                    |
| Time                     | time.Time        | Time                       | int64                     |
| Attributes               | []label.KeyValue | Attrs                      | map[label.Key]interface{} |
