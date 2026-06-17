# CurrencyService

**CurrencyService** is a Node.js microservice responsible for currency conversion using real exchange rates from the European Central Bank (preloaded from JSON).  
It is part of the **Online Boutique** demo application and exposes its functionality via **gRPC**.

---

## Features

- Converts prices between supported currencies.
- Lists all available currencies.
- Uses gRPC for service-to-service communication.
- Supports distributed tracing and Google Cloud Profiler integration.

---

## Requirements

- Node.js 20+ (recommended)
- `npm` package manager
- gRPC clients for testing or another microservice to call it.

---

## Building and Running Locally

1. **Install dependencies:**

```bash
npm install
```

2. **Set required environment variables:**

At minimum, set the port for the gRPC server:

```bash
export PORT=7000
```

Optional tracing and profiling can be configured (see below).

3. **Run the service:**

```bash
node server.js
```

You should see logs confirming that the service has started:

```
CurrencyService gRPC server started on port 7000
```

---

## Environment Variables

| Variable                 | Description                                                | Required                            | Default                         |
| ------------------------ | ---------------------------------------------------------- | ----------------------------------- | ------------------------------- |
| `PORT`                   | gRPC server port to listen on.                             | ✅ Yes                               | *(No default, must be set)*     |
| `DISABLE_PROFILER`       | Disable Google Cloud Profiler if set.                      | ❌ No                                | Profiler is enabled by default. |
| `ENABLE_TRACING`         | Enable OpenTelemetry tracing by setting to `"1"`.          | ❌ No                                | Tracing is disabled unless set. |
| `COLLECTOR_SERVICE_ADDR` | OpenTelemetry collector gRPC address (if tracing enabled). | Required only if `ENABLE_TRACING=1` | N/A                             |
| `OTEL_SERVICE_NAME`      | Overrides the OpenTelemetry service name for tracing.      | ❌ No                                | `currencyservice`               |

---

## Notes

- **gRPC clients** are needed to interact with this service — it does not expose HTTP endpoints.
- Exchange rates are loaded from a static JSON file (`data/currency_conversion.json`) included in the project.
- Tracing and profiling are fully optional but encouraged for observability in a microservices setup.
