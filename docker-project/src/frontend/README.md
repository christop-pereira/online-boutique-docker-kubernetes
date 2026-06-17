# Frontend Microservice

This is a Go-based frontend microservice for an e-commerce application. It provides routing, session handling, and integration with various backend services such as product catalog, cart, checkout, and more. The service also supports OpenTelemetry tracing and Stackdriver profiling.

## Features

- RESTful endpoints for shopping actions (view products, manage cart, place orders, etc.)
- gRPC-based communication with backend services
- Session and currency management via cookies
- Health check endpoint
- Optional tracing and profiling integrations

---

## Prerequisites

- Go 1.17 or newer
- `protoc` and Go gRPC plugin (if modifying protobufs)
- Environment variables set for all required backend services

---

## Building the Application

To build the binary:

```bash
go build -o frontend
```

To run the service:

```bash
./frontend
```

---

## Environment Variables

The service requires several environment variables to be set, mainly for the addresses of its dependent microservices and optional runtime configurations:

| Variable Name            | Description                                 | Default  | Required                 |
| ------------------------ | ------------------------------------------- | -------- | ------------------------ |
| `PORT`                   | Port for the HTTP server to listen on       | `8080`   | No                       |
| `LISTEN_ADDR`            | IP address to bind the HTTP server to       | `""`     | No                       |
| `BASE_URL`               | Optional base URL prefix for all routes     | `""`     | No                       |
| `ENABLE_TRACING`         | Set to `1` to enable OpenTelemetry tracing  | Disabled | No                       |
| `COLLECTOR_SERVICE_ADDR` | OTLP collector address for exporting traces | -        | Yes (if tracing enabled) |
| `ENABLE_PROFILER`        | Set to `1` to enable Stackdriver Profiler   | Disabled | No                       |

### Required Service Addresses

The following variables must always be set to point to the correct gRPC endpoints of the backend services:

| Variable Name                     | Description                               |
| --------------------------------- | ----------------------------------------- |
| `PRODUCT_CATALOG_SERVICE_ADDR`    | Address of the Product Catalog service    |
| `CURRENCY_SERVICE_ADDR`           | Address of the Currency service           |
| `CART_SERVICE_ADDR`               | Address of the Cart service               |
| `RECOMMENDATION_SERVICE_ADDR`     | Address of the Recommendation service     |
| `CHECKOUT_SERVICE_ADDR`           | Address of the Checkout service           |
| `SHIPPING_SERVICE_ADDR`           | Address of the Shipping service           |
| `AD_SERVICE_ADDR`                 | Address of the Advertisement service      |
| `SHOPPING_ASSISTANT_SERVICE_ADDR` | Address of the Shopping Assistant service |

If any of these are missing, the application will panic at startup.

---

## Endpoints

| Method | Path                  | Description                  |
| ------ | --------------------- | ---------------------------- |
| GET    | `/`                   | Home page                    |
| GET    | `/product/{id}`       | Product detail page          |
| GET    | `/cart`               | View cart                    |
| POST   | `/cart`               | Add item to cart             |
| POST   | `/cart/empty`         | Empty cart                   |
| POST   | `/setCurrency`        | Set currency                 |
| GET    | `/logout`             | Clear session                |
| POST   | `/cart/checkout`      | Checkout cart                |
| GET    | `/assistant`          | Shopping assistant interface |
| POST   | `/bot`                | Chatbot interaction          |
| GET    | `/product-meta/{ids}` | Product metadata             |
| GET    | `/robots.txt`         | Disallow bots                |
| GET    | `/_healthz`           | Health check endpoint        |
| Static | `/static/`            | Static assets                |

---

## Logging

Structured JSON logging is configured using `logrus`, with severity and timestamp fields.

---

## Notes

- OpenTelemetry and Stackdriver Profiler integrations are optional and only initialized if the corresponding environment variables are set.
- Tracing is exported using OTLP over gRPC.
- Profiler requires proper GCP credentials if not running on Google Cloud.
