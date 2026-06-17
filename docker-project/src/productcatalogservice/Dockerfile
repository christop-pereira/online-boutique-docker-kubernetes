FROM golang:1.24 AS build

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./
RUN go build -o productcatalogservice .

FROM debian:bookworm-slim

RUN adduser appuser

WORKDIR /app

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

COPY --from=build /app/productcatalogservice .
COPY --from=build /app/products.json .

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-3550} || exit 1

USER appuser

ENTRYPOINT ["./productcatalogservice"]


