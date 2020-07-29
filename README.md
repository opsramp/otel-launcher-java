[![Apache-2.0 license](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

# Lightstep OpenTelemetry Configuration Layer for Java

_NOTE: the code in this repo is currently in alpha and will likely change_

This is the Lightstep package for configuring OpenTelemetry

## Configuration

###  System properties and environmental variables
Supported system properties and environmental variables:

| System property                  | Environment variable             | Purpose                         | Default              | 
|----------------------------------|----------------------------------|---------------------------------|----------------------|       
| ls.service.name                  | LS_SERVICE_NAME                  | Service name                    |                      |                        
| ls.service.version               | LS_SERVICE_VERSION               | Service version                 |                      |                        
| ls.access.token                  | LS_ACCESS_TOKEN                  | Token for Lightstep access      |                      |                        
| otel.exporter.otlp.span.endpoint | OTEL_EXPORTER_OTLP_SPAN_ENDPOINT | Satellite URL                   | ingest.lightstep.com |
| otel.exporter.otlp.span.insecure | OTEL_EXPORTER_OTLP_SPAN_INSECURE | Use insecure transport or not   | false                |
| otel.propagators                 | OTEL_PROPAGATORS                 | Propagator                      | b3                   |
| otel.log.level                   | OTEL_LOG_LEVEL                   | Log level for agent             | info                 |

## Agent
The Lightstep OpenTelemetry Agent is a configuration layer over OpenTelemetry Instrumentation Agent.

### Run

Configuration parameters are passed as Java system properties (-D flags) or as environment variables.

#### Configuration via Java system properties

```shell script
export LS_ACCESS_TOKEN=your-token

java -javaagent:path/to/lightstep-opentelemetry-javaagent-<version>.jar \
     -Dls.service.name=your-service-name
     -Dotel.exporter.otlp.span.endpoint=ingest.staging.lightstep.com \
     -jar myapp.jar
```

#### Configuration via environment variables

```shell script
export LS_ACCESS_TOKEN=your-token
export LS_SERVICE_NAME=your-service-name
export OTEL_EXPORTER_OTLP_SPAN_ENDPOINT=ingest.staging.lightstep.com

java -javaagent:path/to/lightstep-opentelemetry-javaagent-<version>.jar \
     -jar myapp.jar
```


## Launcher

The Lightstep OpenTelemetry Launcher is a configuration layer over OpenTelemetry OTLP trace exporter.

### Installation

pom.xml

```xml
<dependency>
    <groupId>com.lightstep.opentelemetry</groupId>
    <artifactId>opentelemetry-launcher</artifactId>
    <version>VERSION</version>
</dependency>
```

### Usage

#### Easy initialization

```java
// Installs exporter into tracer SDK default provider with batching span processor.
OpenTelemetryConfiguration.newBuilder()
                      .setServiceName("{service_name}")
                      .setAccessToken("{your_access_token}")
                      .setSpanEndpoint("{lightstep_host}")
                      .install();

// Get tracer
Tracer tracer = OpenTelemetry.getTracer("instrumentation-library-name", "1.0.0");
```

#### Manual configuration

```java
// Create builder
Builder builder = OpenTelemetryConfiguration.newBuilder()
                      .setServiceName("{service_name}")
                      .setAccessToken("{your_access_token}")
                      .setSpanEndpoint("{lightstep_host}");

// Instantiate the exporter
OtlpGrpcSpanExporter exporter = builder.buildExporter();

// Add Span Processor with exporter
OpenTelemetrySdk.getTracerProvider()
                      .addSpanProcessor(SimpleSpanProcessor.newBuilder(exporter).build());

// Get tracer
Tracer tracer = OpenTelemetry.getTracer("instrumentation-library-name", "1.0.0");
```

## License

[Apache 2.0 License](./LICENSE).