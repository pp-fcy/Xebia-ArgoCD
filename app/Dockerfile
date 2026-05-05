# Minimal container image for the Hello World service.
#
# Two-stage build: build in a full Python image, run in distroless Python to
# reduce runtime image size and attack surface.
FROM python:3.12-slim AS builder

WORKDIR /app
COPY server.py /app/server.py

FROM gcr.io/distroless/python3-debian12:nonroot

WORKDIR /app
COPY --from=builder /app/server.py /app/server.py

# Cloud Run injects PORT at runtime; 8080 is the default and what the
# Terraform startup/liveness probes hit.
ENV PORT=8080
EXPOSE 8080

# Used by container runtimes (local Docker, docker-compose).
# Cloud Run uses Terraform startup/liveness probes instead.
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:${PORT}/healthz')"

ENTRYPOINT ["python", "-u", "/app/server.py"]
