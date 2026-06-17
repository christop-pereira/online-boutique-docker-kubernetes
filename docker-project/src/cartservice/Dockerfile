FROM mcr.microsoft.com/dotnet/sdk:9.0 AS builder

WORKDIR /app

COPY . .

RUN dotnet restore ./src/
RUN dotnet build ./src/cartservice.csproj -c Release -o /build

FROM mcr.microsoft.com/dotnet/aspnet:9.0

RUN useradd appuser

# Install grpc health probe
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/latest/download/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe

WORKDIR /app

COPY --from=builder /build .

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD grpc_health_probe -addr=localhost:${PORT:-7070} || exit 1

USER appuser

ENTRYPOINT ["dotnet", "cartservice.dll"]