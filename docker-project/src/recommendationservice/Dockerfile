FROM python:3.10 AS builder

WORKDIR /app

COPY requirements.txt .
RUN pip install --upgrade pip \
  && pip install --no-cache-dir -r requirements.txt

FROM python:3.10-slim

RUN adduser appuser

WORKDIR /app

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

COPY demo_pb2_grpc.py demo_pb2.py logger.py recommendation_server.py ./
COPY --from=builder /usr/local/lib/python3.10/site-packages/ /usr/local/lib/python3.10/site-packages/

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-8082} || exit 1

USER appuser

ENTRYPOINT [ "python", "recommendation_server.py" ]
