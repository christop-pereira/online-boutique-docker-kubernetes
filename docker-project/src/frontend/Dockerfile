FROM golang:1.26 AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./
RUN go build -o frontend .

FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y curl \
 && rm -rf /var/lib/apt/lists/*

RUN adduser appuser

WORKDIR /app

COPY /templates ./templates
COPY /static ./static
COPY --from=builder /app/frontend ./

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD curl -f http://localhost:8080/_healthz || exit 1

USER appuser

ENTRYPOINT [ "/app/frontend" ]