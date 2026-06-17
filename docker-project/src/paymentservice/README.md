# Payment Service

This is a Node.js gRPC microservice responsible for handling payment operations in a distributed e-commerce system. It supports optional integrations with Stackdriver Profiler and OpenTelemetry Tracing.

## Features

- gRPC server implementation for payment processing
- Integration with Google Cloud Profiler (optional)
- Integration with OpenTelemetry and OTLP-compatible tracing collectors (optional)
- Structured logging using a custom logger

---

## Prerequisites

- Node.js (version 16.x or later recommended)
- `npm` (Node.js package manager)

---

## Installation

Clone the repository and install dependencies:

```bash
npm install
```

---

## Running the Application

To start the service:

```bash
node index.js
```

Ensure all required environment variables are set beforehand.

---

## Environment Variables

| Variable Name            | Description                                             | Default          | Required                 |
| ------------------------ | ------------------------------------------------------- | ---------------- | ------------------------ |
| `PORT`                   | Port for the gRPC server to listen on                   | None             | Yes                      |
| `DISABLE_PROFILER`       | If set, disables Stackdriver Profiler integration       | Not set          | No                       |
| `ENABLE_TRACING`         | Set to `1` to enable OpenTelemetry tracing              | Disabled         | No                       |
| `COLLECTOR_SERVICE_ADDR` | OTLP-compatible collector endpoint for exporting traces | None             | Yes (if tracing enabled) |
| `OTEL_SERVICE_NAME`      | Name of the service reported to tracing system          | `paymentservice` | No                       |

---

## Tracing and Observability

### Stackdriver Profiler

Stackdriver Profiler is enabled by default. To disable it, set:

```bash
export DISABLE_PROFILER=1
```

### OpenTelemetry Tracing

To enable tracing:

```bash
export ENABLE_TRACING=1
export COLLECTOR_SERVICE_ADDR="http://otel-collector:4317"
```

You can also optionally define the service name:

```bash
export OTEL_SERVICE_NAME="paymentservice"
```

---

## Notes

- The service expects valid `.proto` definitions under the `proto/` directory.
- Logging is structured and output in JSON format to standard output.
