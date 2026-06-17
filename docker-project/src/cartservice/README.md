# CartService

**CartService** is a .NET 9 microservice responsible for storing and retrieving shopping cart data for users.  
It uses **Redis** as its backend storage and communicates via **gRPC**.

---

## Prerequisites

- [.NET 9 SDK](https://dotnet.microsoft.com/)
- [Redis](https://redis.io/) instance (local or container)

---

## Building the Service

Restore dependencies and build:

```bash
dotnet restore
dotnet build src/cartservice.csproj -c Release
```

This will compile the microservice into a ready-to-run executable.

---

## Running Locally

1. **Start Redis**

Make sure Redis is running locally, or use Docker:

```bash
docker run -d --name redis -p 6379:6379 redis
```

2. **Configure Redis Connection**

By default, CartService expects Redis at:

```
localhost:6379
```

You can override this by setting the environment variable:

```bash
export REDIS_ADDR="your_redis_host:port"
```

3. **Run the Service**

```bash
dotnet run --project src/cartservice.csproj
```

The service will start and bind to the configured gRPC port (usually `5000`).

---

## Running in Docker

A `Dockerfile` should be created to containerize this service.  
Once available, you would typically:

```bash
docker build -t cartservice:latest src/
docker run -d --name cartservice \
  -e REDIS_ADDR=redis:6379 \
  -p 8080:8080 \
  cartservice:latest
```

Make sure `redis` is reachable from your container!

---

## Environment Variables

| Variable     | Description                      | Default          |
| ------------ | -------------------------------- | ---------------- |
| `REDIS_ADDR` | Redis server address (host:port) | `localhost:6379` |

---

## Notes

- CartService uses gRPC for service communication — you will need a gRPC-compatible client to interact with it.
- Redis must be available before starting the service.
