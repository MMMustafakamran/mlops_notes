# MLOps Complete Cheat Sheet

## 🛠️ S1: Foundational DevOps & Tools

### Version Control & Collaboration

**Git Commands:**

```bash
git init                    # Initialize repository
git add <file>             # Stage changes
git commit -m "message"    # Commit changes
git push origin <branch>   # Push to remote
git pull origin <branch>   # Pull from remote
git branch <name>          # Create branch
git merge <branch>         # Merge branch
```

**GitHub Actions:**

- **Workflow File:** `.github/workflows/main.yml`
- **Triggers:** `on: [push, pull_request]`, `on: workflow_dispatch`
- **Secrets:** `${{ secrets.SECRET_NAME }}`
- **Basic Structure:**

```yaml
name: CI/CD Pipeline
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run tests
        run: pytest
```

### Development & Automation

**Makefile:**

```makefile
.PHONY: test lint install
install:
    pip install -r requirements.txt
lint:
    flake8 *.py
test:
    pytest tests/
```

**Linting & Testing:**

- `flake8 .` - Check code quality
- `pytest tests/` - Run unit tests
- `pytest -v` - Verbose output

**Flask REST API:**

```python
from flask import Flask, jsonify
app = Flask(__name__)
@app.route('/predict', methods=['POST'])
def predict():
    return jsonify({'result': 'value'})
```

### Docker

**Dockerfile Basics:**

```dockerfile
FROM python:3.9-slim
WORKDIR /app
ENV PYTHONUNBUFFERED=1
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

**Docker Commands:**

```bash
docker build -t image:tag .        # Build image
docker run -p 5000:5000 image:tag  # Run container
docker push repo/image:tag         # Push to registry
docker ps                          # List containers
docker logs <container-id>         # View logs
```

**Docker Compose:**

```yaml
version: "3.8"
services:
  flask-app:
    build: .
    ports:
      - "5000:5000"
    environment:
      - DB_HOST=mongodb
    networks:
      - app-network
    depends_on:
      - mongodb

  mongodb:
    image: mongo:latest
    volumes:
      - mongo-data:/data/db
    networks:
      - app-network

volumes:
  mongo-data:

networks:
  app-network:
    driver: bridge
```

**Commands:**

```bash
docker-compose up -d        # Start services
docker-compose down         # Stop services
docker-compose logs -f      # View logs
```

### Jenkins CI/CD

**Pipeline Structure:**

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Lint & Test') {
            steps {
                sh 'flake8 .'
                sh 'pytest'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t app:${BUILD_NUMBER} .'
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(...)]) {
                    sh 'docker push app:${BUILD_NUMBER}'
                }
            }
        }
        stage('Deploy') {
            steps { sh './deploy.sh' }
        }
    }
}
```

---

## ☸️ S2: Kubernetes, MLOps & Experiment Tracking

### Kubernetes Architecture

**Control Plane Components:**

- **API Server:** Frontend for K8s control plane
- **Scheduler:** Assigns pods to nodes
- **Controller Manager:** Runs controller processes
- **etcd:** Key-value store for cluster state

**Worker Node Components:**

- **Kubelet:** Ensures containers are running in pods
- **Kube-proxy:** Network proxy maintaining network rules
- **Pods:** Smallest deployable units

### Core K8s Objects

**Pod:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: app
      image: myapp:latest
      ports:
        - containerPort: 5000
```

**Deployment:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: app
          image: myapp:latest
```

**Service:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: LoadBalancer
```

**ConfigMap:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DATABASE_URL: "mongodb://db:27017"
```

**Secret:**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  password: <base64-encoded>
```

**Ingress:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: myapp.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

**kubectl Commands:**

```bash
kubectl apply -f config.yaml      # Apply configuration
kubectl get pods                  # List pods
kubectl get services              # List services
kubectl describe pod <name>       # Pod details
kubectl logs <pod-name>           # View logs
kubectl delete pod <name>         # Delete pod
kubectl scale deployment <name> --replicas=5
```

### MLOps Fundamentals

**ML Lifecycle:**
Data → Pre-processing → Training → Evaluation → Deployment

**Maturity Levels:**

- **Level 0:** Manual, script-based, no versioning
- **Level 1:** Automated pipelines, model registry, metadata stores
- **Level 2:** Full CI/CD with auto-retraining on drift detection

**DevOps vs MLOps:**

- DevOps: System monitoring, uptime, performance
- MLOps: Model performance, data drift, concept drift

### Data Version Control (DVC)

**Setup & Commands:**

```bash
dvc init                           # Initialize DVC
dvc add data/dataset.csv           # Track data file
git add data/dataset.csv.dvc .gitignore
dvc remote add -d storage s3://mybucket
dvc push                           # Push to remote storage
dvc pull                           # Pull from remote storage
dvc checkout                       # Restore tracked files
```

**Pipeline (dvc.yaml):**

```yaml
stages:
  preprocess:
    cmd: python preprocess.py
    deps:
      - data/raw.csv
    outs:
      - data/processed.csv

  train:
    cmd: python train.py
    deps:
      - data/processed.csv
      - train.py
    params:
      - train.learning_rate
      - train.epochs
    outs:
      - model.pkl
    metrics:
      - metrics.json:
          cache: false
```

**Pipeline Commands:**

```bash
dvc repro                  # Reproduce pipeline
dvc dag                    # Show pipeline DAG
dvc metrics show           # Show metrics
dvc params diff            # Compare parameters
```

### MLflow

**Tracking:**

```python
import mlflow

mlflow.set_tracking_uri("http://localhost:5000")
mlflow.set_experiment("my-experiment")

with mlflow.start_run():
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_metric("accuracy", 0.95)
    mlflow.log_artifact("model.pkl")
    mlflow.sklearn.log_model(model, "model")
```

**Auto Logging:**

```python
mlflow.autolog()  # Automatically logs params, metrics, models
```

**Model Registry:**

```python
# Register model
mlflow.register_model("runs:/<run-id>/model", "MyModel")

# Transition model stage
client = mlflow.tracking.MlflowClient()
client.transition_model_version_stage(
    name="MyModel",
    version=1,
    stage="Production"
)
```

**Stages:** None → Staging → Production → Archived

**MLproject File:**

```yaml
name: My Project
conda_env: conda.yaml
entry_points:
  main:
    parameters:
      learning_rate: { type: float, default: 0.01 }
    command: "python train.py {learning_rate}"
```

**Commands:**

```bash
mlflow ui                          # Start UI server
mlflow run . -P learning_rate=0.01 # Run project
mlflow models serve -m models:/MyModel/Production
```

---

## 📈 S3: Monitoring & Workflow Management

### Apache Airflow

**DAG Structure:**

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def task_function():
    print("Executing task")

with DAG(
    'my_dag',
    start_date=datetime(2024, 1, 1),
    schedule_interval='@daily',
    catchup=False
) as dag:

    task1 = PythonOperator(
        task_id='task_1',
        python_callable=task_function
    )

    task2 = PythonOperator(
        task_id='task_2',
        python_callable=task_function
    )

    task1 >> task2  # Set dependency
```

**Key Concepts:**

- **DAG:** Directed Acyclic Graph (no cycles)
- **Operators:** PythonOperator, BashOperator, etc.
- **Schedule:** `@daily`, `@hourly`, cron expressions
- **Dependencies:** `task1 >> task2` or `task1.set_downstream(task2)`

**Commands:**

```bash
airflow db init                    # Initialize database
airflow webserver                  # Start web UI
airflow scheduler                  # Start scheduler
airflow dags list                  # List DAGs
airflow tasks test <dag> <task> <date>
```

### Prometheus

**Architecture:**

- Time-series database for metrics
- Pull-based model (scrapes targets)
- PromQL query language

**prometheus.yml:**

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "my-app"
    static_configs:
      - targets: ["localhost:8000"]
```

**Common Metrics:**

- Counter: Monotonically increasing (requests_total)
- Gauge: Can go up/down (memory_usage)
- Histogram: Distribution of values (request_duration)
- Summary: Similar to histogram with percentiles

**Python Client:**

```python
from prometheus_client import Counter, Gauge, Histogram
from prometheus_client import start_http_server

requests = Counter('requests_total', 'Total requests')
memory = Gauge('memory_usage_bytes', 'Memory usage')
latency = Histogram('request_duration_seconds', 'Request latency')

requests.inc()  # Increment counter
memory.set(1024)  # Set gauge value
latency.observe(0.5)  # Record observation
```

**PromQL Queries:**

```promql
rate(requests_total[5m])           # Request rate over 5min
avg(memory_usage_bytes)            # Average memory
histogram_quantile(0.95, latency)  # 95th percentile
```

### ML Drift Detection

**Data Drift:**

- Definition: Changes in input data distribution (P(X))
- Example: Training on summer data, predicting in winter
- Detection: Statistical tests (KS test, Chi-square), Population Stability Index (PSI)

**Concept Drift:**

- Definition: Changes in relationship between input and output (P(Y|X))
- Example: Customer behavior changes over time
- Detection: Monitor model performance metrics over time

**Detection Strategies:**

```python
# Statistical test example
from scipy.stats import ks_2samp

statistic, p_value = ks_2samp(train_data, production_data)
if p_value < 0.05:
    print("Drift detected!")

# Model performance monitoring
if current_accuracy < threshold:
    trigger_retraining()
```

**Mitigation:**

- Regular model retraining
- Ensemble methods
- Online learning
- A/B testing new models

---

## Quick Reference Commands

**Git:** `init, add, commit, push, pull, branch, merge`  
**Docker:** `build, run, push, ps, logs, exec`  
**Docker Compose:** `up, down, logs, ps`  
**kubectl:** `apply, get, describe, logs, delete, scale`  
**DVC:** `init, add, push, pull, repro, metrics show`  
**MLflow:** `ui, run, models serve`  
**Airflow:** `db init, webserver, scheduler, dags list`

---

## Key Concepts Summary

- **CI/CD:** Automated testing and deployment pipeline
- **Containerization:** Docker for consistent environments
- **Orchestration:** Kubernetes for scaling and managing containers
- **Version Control:** Git for code, DVC for data/models
- **Experiment Tracking:** MLflow for reproducibility
- **Workflow Management:** Airflow for complex pipelines
- **Monitoring:** Prometheus for metrics, drift detection for model health
- **MLOps Maturity:** Progress from manual (L0) to fully automated (L2)
