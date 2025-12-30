## **MLflow**

**Projects & Tracking**

- **MLflow Projects:** Package code for reuse & reproducibility.
- **Run commands:** e.g., `python 1-tracking.py`; use local or virtual env.
- **Experiments:** Name them to avoid conflicts.
- **Remote repos:** Can run projects from GitHub; support parameters.

**Models**

- Wraps models from PyTorch, sklearn, etc. → unified interface.
- Uniform inference: all models predict the same way.
- Supports custom models too.

**Model Registry**

- Central store: sharing, versioning, stage management.
- Stages: **Staging → Production → Archived**.
- Metadata: versions, stages, tags, aliases.

**Workflow**

- `with mlflow.start_run()`: start run.
- Log hyperparameters: `log_params`, metrics: `log_metrics`, artifacts: `log_artifact`.
- `infer_signature`: captures input/output schema.
- `mlflow.sklearn.log_model`: save model + metadata.

**MLProject File**

- **Env:** `conda.yml`, `requirements.txt`.
- **Entry points:** define scripts to run.
- **Parameters:** e.g., `n_estimators`, `max_depth`.

**Loading Models**

- Model URI: `runs:/<run_id>/model`.
- Load: `mlflow.sklearn.load_model`, `mlflow.pytorch.load_model`.
- Custom logic: `mlflow.pyfunc.PythonModel`.

**Registry API**

- `MlflowClient`: manage models programmatically.
- Stage transitions: `transition_model_version`.
- Metadata: `update_registered_model`, `set_model_version_tag`.
- Load latest stage: `models:/<model_name>/Production`.

---

## **Apache Airflow**

**Concepts**

- **DAG:** Directed Acyclic Graph = pipeline.
- **Operators:** run tasks (Python, Bash, etc.).
- **Sensors:** wait for events.
- **Hooks:** connect external services.
- **XCom:** exchange small data between tasks.

**Infrastructure**

- Docker-compose recommended.
- Services: Postgres, scheduler, init.
- Folders: `dags/` for code, `logs/` for runs.

**Coding & Dependencies**

- Tasks linked: `>>` operator.
- Modern decorators: `@dag`, `@task`.
- Dependency types: Linear, Multiple Upstream, Multiple Downstream.

---

## **Monitoring: Prometheus & Grafana**

**Prometheus**

- Pull-based scraping; query via **PromQL**.
- Metric types: **Counter** (count), **Gauge** (value), **Histogram** (duration/magnitude).

**Grafana**

- Visual dashboards from Prometheus data.

**Alerts**

- **Exporters:** provide metrics to Prometheus.
- `alert_rules.yml`: define alert conditions.
- `alert_manager`: send alerts (Slack, etc.).
- **Blackbox exporter:** check endpoint availability.

---

## **ML Drift Detection**

**Types of Drift**

- **Concept Drift:** input → label relationship changes.
- **Data Drift:**

  - **Label Drift:** label distribution changes.
  - **Feature Drift:** input feature distribution changes.

**Detection**

- Numerical: Mean, StdDev.
- Categorical: Frequency trends.
