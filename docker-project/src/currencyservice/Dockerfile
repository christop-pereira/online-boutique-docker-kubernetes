FROM node:20 AS builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

FROM node:20-alpine

WORKDIR /app

RUN adduser -S appuser

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

COPY /data ./data
COPY /proto ./proto
COPY server.js .

COPY --from=builder /app/node_modules ./node_modules

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-7000} || exit 1

USER appuser

ENTRYPOINT ["node", "server.js"]