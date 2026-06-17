# CheckoutService

**CheckoutService** is a Go-based microservice that handles the core checkout flow of an online store.  
It orchestrates order validation, payment processing, shipping, and email confirmation by calling other microservices via **gRPC**.

---

## Features

- Retrieves cart contents for a user.
- Calculates shipping cost and handles payment.
- Sends order confirmation emails.
- Communicates with:  
  `cartservice`, `currencyservice`, `paymentservice`, `shippingservice`, `productcatalogservice`, `emailservice`.

---

## Requirements

- Go 1.22+ (recommended for Go modules and compatibility)
- Running instances of:
  - Redis (for `cartservice`)
  - Other microservices (`productcatalogservice`, `currencyservice`, etc.)
- gRPC tools (if you need to rebuild protobufs).

---

## Building the Service

Build the Go binary:

```bash
go build -o checkoutservice .
```

This will produce an executable named `checkoutservice` in the current directory.

---

## Running Locally

Before launching the service, you need to provide the addresses of its dependencies via **environment variables**.

Example run:

```bash
export SHIPPING_SERVICE_ADDR=localhost:50051
export PRODUCT_CATALOG_SERVICE_ADDR=localhost:50052
export CART_SERVICE_ADDR=localhost:50053
export CURRENCY_SERVICE_ADDR=localhost:50054
export EMAIL_SERVICE_ADDR=localhost:50055
export PAYMENT_SERVICE_ADDR=localhost:50056

go run .
```

---

## Environment Variables

| Variable                       | Purpose                                              | Required                   | Default  |
| ------------------------------ | ---------------------------------------------------- | -------------------------- | -------- |
| `SHIPPING_SERVICE_ADDR`        | Address of the Shipping Service gRPC endpoint        | ✅ Yes                      | N/A      |
| `PRODUCT_CATALOG_SERVICE_ADDR` | Address of the Product Catalog Service gRPC endpoint | ✅ Yes                      | N/A      |
| `CART_SERVICE_ADDR`            | Address of the Cart Service gRPC endpoint            | ✅ Yes                      | N/A      |
| `CURRENCY_SERVICE_ADDR`        | Address of the Currency Service gRPC endpoint        | ✅ Yes                      | N/A      |
| `EMAIL_SERVICE_ADDR`           | Address of the Email Service gRPC endpoint           | ✅ Yes                      | N/A      |
| `PAYMENT_SERVICE_ADDR`         | Address of the Payment Service gRPC endpoint         | ✅ Yes                      | N/A      |
| `PORT`                         | Port for the gRPC server to bind to                  | ❌ No                       | `5050`   |
| `ENABLE_TRACING`               | Set to `1` to enable OpenTelemetry tracing           | ❌ No                       | Disabled |
| `COLLECTOR_SERVICE_ADDR`       | Address of the OpenTelemetry collector (if tracing)  | Only if `ENABLE_TRACING=1` | N/A      |
| `ENABLE_PROFILER`              | Set to `1` to enable Google Cloud Profiler           | ❌ No                       | Disabled |

---

## Running in Docker

If you package the service with a `Dockerfile`, the container must be started with all the environment variables set:

```bash
docker run -d --name checkoutservice \
  -e SHIPPING_SERVICE_ADDR=shipping:50051 \
  -e PRODUCT_CATALOG_SERVICE_ADDR=productcatalog:50052 \
  -e CART_SERVICE_ADDR=cart:50053 \
  -e CURRENCY_SERVICE_ADDR=currency:50054 \
  -e EMAIL_SERVICE_ADDR=email:50055 \
  -e PAYMENT_SERVICE_ADDR=payment:50056 \
  -p 5050:5050 \
  checkoutservice:latest
```

---

## Notes

- This service uses gRPC — you’ll need gRPC-compatible tools or clients to test it.
- Distributed tracing (via OpenTelemetry) and profiling (via Google Cloud Profiler) are optional and configurable.
- It requires all its dependencies to be available at startup, or it will fail to connect and crash.
