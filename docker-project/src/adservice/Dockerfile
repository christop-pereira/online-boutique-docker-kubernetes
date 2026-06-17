# --- Compilation with JDK ---
FROM eclipse-temurin:21-jdk AS builder

WORKDIR /app

COPY . .

RUN ./gradlew installDist

# --- Compilation with JRE ---
FROM eclipse-temurin:21-jre

RUN useradd appuser

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

WORKDIR /app

COPY --from=builder /app/build/install/hipstershop .

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-9555} || exit 1

USER appuser

ENTRYPOINT ["/app/bin/AdService"]