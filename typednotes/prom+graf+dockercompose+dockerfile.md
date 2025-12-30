# ML Model Monitoring with Prometheus, FastAPI, and Docker

## Overview

This guide covers setting up a FastAPI ML inference service with Prometheus metrics monitoring, containerized using Docker and Docker Compose, with Grafana for visualization.

---

## Complete Code Files

### 1. FastAPI Application (app.py)

```python
from fastapi import FastAPI, Response
from prometheus_client import Counter, Histogram, generate_latest
import time
import random

app = FastAPI()

# ---------------------------
# PROMETHEUS METRICS
# ---------------------------

# Counter: monotonically increasing metric (requests, errors, etc.)
# Constructor: (metric_name, description, [labels])
requests = Counter(
    "model_req_total",
    "total number of inference requests",
    ["model_name", "version"]  # labels for filtering (e.g., v1, v2)
)

# Histogram: measures distributions (latency, size, etc.)
# Auto-creates _bucket, _count, and _sum metrics
latency = Histogram(
    "model_latency_seconds",
    "inference latency in seconds",
    ["model_name"]
)

# Counter for tracking errors
errors = Counter(
    "total_error",
    "total amount of errors",
    ["model_name"]
)

# ---------------------------
# API ENDPOINTS
# ---------------------------

@app.post("/predict")
def predict(data: dict):
    """ML inference endpoint with metrics tracking"""

    start = time.time()

    # Increment request counter
    requests.labels(
        model_name="fraud_model",
        version="v1"
    ).inc()

    # Simulate 10% random failure rate
    if random.random() < 0.1:
        errors.labels(model_name="fraud_model").inc()

    # Record request latency
    latency.labels(model_name="fraud_model").observe(time.time() - start)

    return {"prediction": "fraud_detected"}


@app.get("/metrics")
def metrics():
    """Expose metrics for Prometheus scraping"""
    return Response(
        generate_latest(),      # collect all metrics
        media_type="text/plain"
    )
```

---

### 2. Prometheus Configuration (prometheus.yml)

```yaml
# Prometheus scraping configuration

global:
  scrape_interval: 5s # poll targets every 5 seconds

scrape_configs:
  - job_name: "ml-model-service" # job identifier in Prometheus UI

    static_configs:
      - targets:
          - "localhost:8000" # scrape /metrics from model API
```

---

### 3. Dockerfile for ML Model Service

```dockerfile
# Build image for ML model API

FROM python:3.10

WORKDIR /app

# Copy application code
COPY . /app

# Install Python dependencies
RUN pip install fastapi uvicorn prometheus_client

# Container listens on port 8000
EXPOSE 8000

# Start uvicorn server
# Format: uvicorn <module>:<app_instance> --host <host> --port <port>
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

### 4. Docker Compose Configuration (docker-compose.yml)

```yaml
# Multi-container orchestration: API + Prometheus + Grafana

version: "3"

services:
  # ML Model API
  model-api:
    image: python:3.10
    container_name: model-api
    volumes:
      - .:/app # mount current directory to /app (live code reload)
    working_dir: /app
    command: >
      bash -c "pip install fastapi uvicorn prometheus_client && 
               uvicorn app:app --host 0.0.0.0 --port 8000"
    ports:
      - "8000:8000" # host:container port mapping

  # Prometheus monitoring
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml # inject config file
    ports:
      - "9090:9090"
    # Uses default CMD from official image

  # Grafana visualization
  grafana:
    image: grafana/grafana:9.0
    container_name: grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus # start after Prometheus is ready
```

---

## Metric Types Overview

### Prometheus Metric Classes

**Counter:**

- Only increases (never decreases)
- Use cases: request counts, errors, completed jobs
- Operations: `.inc()` to increment

**Histogram:**

- Tracks distribution of values
- Use cases: latency, request size, response time
- Auto-creates: `_bucket`, `_count`, `_sum`
- Operations: `.observe(value)` to record

**Gauge:**

- Can increase or decrease
- Use cases: memory usage, active connections, temperature

---

## Quick Reference

### Python/Prometheus Operations

```python
# Define Counter
REQUESTS = Counter("metric_name", "Description", ["label1", "label2"])

# Increment with labels
REQUESTS.labels(label1="value1", label2="value2").inc()

# Define and use Histogram
LATENCY = Histogram("latency_seconds", "Time spent", ["endpoint"])
LATENCY.labels(endpoint="/predict").observe(0.123)
```

### Dockerfile Commands

- `FROM` → base image
- `WORKDIR` → set working directory in container
- `COPY` → copy files from host to container
- `RUN` → execute commands during build
- `EXPOSE` → document port (informational)
- `CMD` → default command when container starts
- `ENV` → set environment variables

### Docker Compose Syntax

- `version` → compose file format
- `services` → container definitions
- `build: .` → build from local Dockerfile
- `image` → use pre-built image
- `ports` → map host:container ports
- `volumes` → mount host:container paths
- `depends_on` → startup order control
- `command` → override default container command

---

## Volume Mounting

**Format:** `host_path:container_path`

Example: `./prometheus.yml:/etc/prometheus/prometheus.yml`

- Left side: file location on your computer
- Right side: where container expects the file
- File persists on host, container reads/writes through mount

---

## Access Points

- **ML Model API**: http://localhost:8000
- **Metrics Endpoint**: http://localhost:8000/metrics
- **Prometheus UI**: http://localhost:9090
- **Grafana Dashboard**: http://localhost:3000

---

## Running the Stack

```bash
# Start all services
docker-compose up

# Stop all services
docker-compose down

# Rebuild and start
docker-compose up --build
```
