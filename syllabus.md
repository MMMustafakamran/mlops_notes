# 🎯 Complete MLOps Final Exam Syllabus
---

## 🛠️ S1: Foundational DevOps & Tools

### **1. Version Control & Collaboration**

- **Basic Git Commands**: `init`, `add`, `commit`, `push`, `pull`, `branch`, `merge`
- **GitHub Actions**: CI/CD workflows, YAML syntax, triggers, secret management

### **2. Development & Automation**

- **Makefile**: Automation of repetitive tasks, `.PHONY` targets
- **Linting**: Using `flake8` for Python code quality
- **Pytest**: Writing and running unit tests
- **Flask App Development**: Building REST APIs

### **3. Containerization with Docker**

- **Docker Foundations**:
  - **Dockerfile**: Base images, layers, `ENV`, `WORKDIR`
  - **Docker Compose**:
    - Orchestrating multi-container apps
    - Service Configuration (Flask + MongoDB services, environment variables)
    - Data Persistence: Named volumes for MongoDB data
    - Networking: Custom bridge networks for inter-service communication
    - `docker-compose.yml`: Version, services, volumes, networks sections
  - **Docker Hub**: Registry for sharing images
  - **Volumes & Networks**: Data persistence and inter-container communication

### **4. Cloud & Deployment**

- **Azure**: Cloud basics and deployment concepts

### **5. CI/CD Pipeline**

- **Jenkins** (Traditional CI/CD server):
  - **Pipeline Structure**: Stages (Checkout → Lint&Test → Build → Push → Deploy)
  - **Credential Management**: Secure credential storage and usage
  - **Build Automation**: Docker image building with dynamic tagging
  - **Deployment**: Script-based deployment integration
  - Pipelines and agents

---

## ☸️ S2: Kubernetes, MLOps & Experiment Tracking

### I. Kubernetes (K8s) & Container Orchestration

- **Introduction to K8s:** Google's orchestration tool for microservices.
- **Core Pillars:** High Availability, Scalability, Performance, and Disaster Recovery.
- **Architecture:**
  - **Control Plane (Master):** API Server, Controller Manager, Scheduler, etcd (state store).
  - **Worker Nodes:** Kubelet, Kube-proxy, Pods.
- **Core Objects:**
  - **Pods:** Smallest deployable units; abstraction over containers.
  - **Services (Svc):** Stable networking and load balancing for pods.
  - **Ingress:** External access layer.
  - **Volumes:** Persistent storage (K8s is stateless by default).
- **Configuration:**
  - **Config-Map & Secrets:** Storing env vars and sensitive data.
  - **Deployments vs. StatefulSets:** Stateless vs. stateful workloads.

### II. MLOps Fundamentals

- **The ML Lifecycle:** Data ➡️ Pre-processing ➡️ Training ➡️ Evaluation ➡️ Deployment.
- **DevOps vs. MLOps:** Systems monitoring vs. Model performance/drift monitoring.
- **Maturity Levels:**
  - **Level 0:** Manual processes, script-based, no versioning.
  - **Level 1:** Automated pipelines, Model Registry, metadata stores.
  - **Level 2:** Full CI/CD automation with automatic retraining on performance drops.

### III. Data Version Control (DVC)

- **Git Integration:** Tracking large binary data without bloating Git history.
- **.dvc Files:** Tracking metadata/hashes in Git while data stays in remote storage.
- **Remote Storage:** Connecting to S3, GDrive, or local remotes.
- **Pipelines:** Defining stages in `dvc.yaml` and using `dvc repro` for atomic reruns.

### IV. CI/CD for Machine Learning

- **GitHub Actions for ML:** Triggering training on PRs, commenting on metrics changes.

### V. Experiment Tracking with MLflow

- **Core Concept:** Tracking params, metrics, and artifacts for reproducibility.
- **Components:**
  - **Tracking:** Logging runs manually or using Auto Log.
  - **Model Registry:** Versioning models and managing stage transitions (Production, Staging).
  - **Projects:** Using `MLproject` and `Conda.yaml` for environment reproducibility.

---

## 📈 S3: Monitoring & Workflow Management

- **Apache Airflow:** Workflow orchestration using DAGs (Direct Acyclic Graphs).
- **Prometheus:** Time-series database for monitoring system and application health.
- **ML Drift:**
  - **Data Drift:** Changes in input data distribution.
  - **Concept Drift:** Changes in the relationship between input and output.
