# Email Service Microservice

This is a gRPC-based email confirmation microservice. It is designed to send confirmation emails (currently in **dummy mode only**) using a templated HTML file. It includes health checks and optional integration with Google Cloud Profiler and OpenTelemetry for tracing.

---

## Features

- gRPC service for sending order confirmation emails.
- Email template rendering with Jinja2.
- Google Cloud Profiler support.
- OpenTelemetry tracing support.
- Health check endpoint.

---

## Requirements

- Python 3.7+
- `pip` (Python package installer)

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Building and Running

To start the service in dummy mode (no actual email will be sent):

```bash
python email_server.py
```

This will:

- Start a gRPC server on port `8080` by default.
- Load the email confirmation HTML template from the `templates/` directory.
- Optionally initialize Stackdriver Profiler and tracing depending on environment variables.

---

## Environment Variables

| Variable Name            | Description                                            | Default            |
| ------------------------ | ------------------------------------------------------ | ------------------ |
| `PORT`                   | Port the server listens on.                            | `8080`             |
| `DISABLE_PROFILER`       | If set, disables Stackdriver Profiler integration.     | Not set (enabled)  |
| `GCP_PROJECT_ID`         | Google Cloud project ID used for Stackdriver Profiler. | Not required       |
| `ENABLE_TRACING`         | Set to `1` to enable OpenTelemetry tracing.            | Not set (disabled) |
| `COLLECTOR_SERVICE_ADDR` | OTLP collector endpoint for tracing export.            | `localhost:4317`   |

---

## Notes

- **Production email sending is not implemented.** The service will raise an exception if run outside of dummy mode.
- Email content is generated via a Jinja2 template (`confirmation.html`) and rendered with order details.

---

## Health Check

Implements gRPC health checks through:

- `Check()` — returns `SERVING`
- `Watch()` — returns `UNIMPLEMENTED`
