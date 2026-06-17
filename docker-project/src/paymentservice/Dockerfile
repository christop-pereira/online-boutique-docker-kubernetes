FROM node:16-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

FROM node:16-alpine

WORKDIR /app

RUN adduser -S appuser

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

COPY /proto ./proto
COPY index.js logger.js server.js charge.js ./
COPY --from=builder /app/node_modules ./node_modules

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-50052} || exit 1

USER appuser

ENTRYPOINT [ "node", "index.js" ]
