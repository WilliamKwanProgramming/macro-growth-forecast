# Global GDP Forecasting MLOps Pipeline

An end-to-end, production-ready pipeline for forecasting country-level GDP growth using the IMF World Economic Outlook (WEO) database. The project combines modern MLOps practices (Airflow, DVC, MLflow, Docker, CI/CD) with deep learning models (e.g., LSTMs in TensorFlow) to build reproducible, scalable economic forecasting workflows.

---

## Table of Contents

- [Overview](#overview)
- [Data Source](#data-source)
- [Architecture & Tooling](#architecture--tooling)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Repository Setup](#repository-setup)
  - [Airflow & Docker](#airflow--docker)
  - [DVC Setup](#dvc-setup)
- [Workflow Orchestration](#workflow-orchestration)
- [Modeling & Experiment Tracking](#modeling--experiment-tracking)
- [Serving & Visualization](#serving--visualization)
- [Project Structure](#project-structure)
- [Development Workflow](#development-workflow)
- [Roadmap](#roadmap)
- [License](#license)

---

## Overview

The International Monetary Fund (IMF) plays a central role in stabilizing and monitoring the global financial system through its data and analytics. This project leverages the IMF **World Economic Outlook (WEO)** database to build a robust forecasting pipeline for Gross Domestic Product (GDP) growth across countries and regions.

Key goals:

- Use historical macroeconomic indicators to forecast country-level GDP growth.
- Build a **reproducible, automated MLOps stack** for data ingestion, training, evaluation, and deployment.
- Provide a foundation that is useful to **economists, data scientists, policymakers, and investors**.

At a high level, the system:

1. Ingests and preprocesses IMF WEO data.
2. Engineers features and prepares time series datasets.
3. Trains deep learning models (e.g., LSTM) using TensorFlow.
4. Tracks experiments and artifacts with MLflow and DVC.
5. Orchestrates everything through Airflow and Docker.
6. Exposes models via REST APIs and dashboards.

---

## Data Source

### World Economic Outlook 2024 Database

The primary dataset is the **World Economic Outlook (WEO)** database from the IMF, which contains:

- National accounts (e.g., GDP, growth rates)
- Inflation and unemployment indicators
- Balance of payments
- Fiscal and other macroeconomic indicators

Source:  
[Access the dataset here](https://www.imf.org/en/Publications/SPROLLS/world-economic-outlook-databases#sort=%40imfdate%20descending)

### Data Card

- **Size:** 8,624 rows × 58 columns  
- **File Format:** `.xls`  
- **Organization:** Grouped by *Subject* (indicator) and then by *Country*  

### Core Variables

| Variable Name                 | Role      | Type         | Description                                              |
|------------------------------|-----------|--------------|----------------------------------------------------------|
| WEO Country Code             | ID        | Integer      | Unique numeric identifier for each country               |
| WEO Subject Code             | ID        | String       | Code for GDP-related and macroeconomic time series       |
| Country                      | Feature   | String       | Country/economy name (196 economies)                     |
| Subject Descriptor           | Feature   | Categorical  | Description of the macroeconomic indicator               |
| Units                        | ID        | String       | Units of measurement for the indicator                   |
| Scale                        | ID        | String       | Scaling for units (e.g., millions, billions)             |
| Country/Series-specific notes| ID        | String       | Metadata about sources and adjustments                   |
| Years (1980–2029; multiple)  | Feature   | Continuous   | Time series values per year                              |
| Estimate Start After         | Feature   | Integer      | Year after which observations are estimates              |

---

## Architecture & Tooling

This project is designed as a **modern MLOps system**, leveraging the following stack:

- **Version Control & CI/CD**
  - GitHub (repo, PRs, code review)
  - GitHub Actions (CI/CD, pytest, Docker builds)
- **Experimentation & Data Science**
  - Jupyter Notebook
  - TensorFlow
  - MLflow (experiment tracking)
- **Data & Model Versioning**
  - DVC with a GCP-backed remote
- **Orchestration & Automation**
  - Apache Airflow (DAG orchestration)
  - Docker / Docker Compose
- **Serving & Visualization**
  - FastAPI / Flask (REST APIs)
  - Streamlit (interactive apps)
  - Looker (BI dashboards)
- **Infrastructure**
  - Google Cloud Platform (GCP)

Airflow DAG example:

<img src="assets/airflow_dag.png" alt="Airflow DAG" width="800" height="400">

Airflow logo:

<img src="assets/Logo.jpg" alt="airflow" width="900" height="200">

DVC on GCP:

<img src="assets/DVC_in_GCP.png" alt="DVC in GCP" width="800" height="400">

MLflow experiment views:

<img src="assets/mlflow.PNG" alt="MLflow Run View" width="800" height="400">

<img src="assets/mlfloq1.PNG" alt="MLflow Experiment Tracking" width="800" height="400">

---

## Getting Started

### Prerequisites

Make sure you have the following installed:

- **Python** >= 3.8  
- **Docker** & **Docker Compose**  
- **Google Cloud Platform (GCP)** account & credentials  
- (Optional) Virtual environment tool (e.g., `venv`, `conda`, `poetry`)

### Repository Setup

```bash
git clone https://github.com/namansnghl/World-Econ-Growth-Forecast
cd <repository-directory>
```

Check your Python version:

```bash
python --version
```

Install Python dependencies:

```bash
pip install -r requirements.txt
```

### Airflow & Docker

1. Install Apache Airflow (Celery executor example):

   ```bash
   pip install "apache-airflow[celery]==2.9.1" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.9.1/constraints-3.8.txt"
   ```

   > Update `constraints-3.x` based on your Python 3.x version.

2. Confirm you have enough memory for Docker (recommended ≥ 4 GB):

   ```bash
   docker run --rm "debian:bullseye-slim" bash -c 'numfmt --to iec $(echo $(($(getconf _PHYS_PAGES) * $(getconf PAGE_SIZE))))'
   ```

3. Download the Airflow `docker-compose.yaml`:

   ```bash
   curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.9.1/docker-compose.yaml'
   ```

4. Initialize the Airflow database (first time only):

   ```bash
   docker compose up airflow-init
   ```

5. Start Airflow:

   ```bash
   docker compose up
   ```

6. Open the Airflow UI at [http://localhost:8080](http://localhost:8080) and log in:

   ```text
   user: airflow
   password: airflow
   ```

7. In the Airflow UI, enable and trigger the GDP Forecasting DAG (click the play ▶️ button).

To disable example Airflow DAGs, set this in `docker-compose.yaml`:

```yaml
AIRFLOW__CORE__LOAD_EXAMPLES: 'false'
```

### DVC Setup

Install DVC:

```bash
pip install dvc
```

Initialize DVC in the repo:

```bash
dvc init
```

Track files with DVC:

```bash
dvc add <file-path>
```

Configure your GCP remote (example):

```bash
dvc remote add -d gcp_remote gcs://<your-gcs-bucket>/weo-forecast
dvc push
```

---

## Workflow Orchestration

Airflow orchestrates the full lifecycle:

1. **Data Ingestion:** Download or load the WEO `.xls` files.
2. **Preprocessing:** Clean, transform, and normalize macroeconomic features.
3. **Feature Engineering:** Build time series windows and additional features.
4. **Model Training:** Trigger TensorFlow training jobs.
5. **Evaluation & Logging:** Log metrics and artifacts to MLflow and DVC.
6. **Deployment Hooks:** Optionally trigger container builds or API updates.

Each step is a task in the Airflow DAG, allowing for retries, alerting, and scheduling.

---

## Modeling & Experiment Tracking

### TensorFlow Models

The project uses TensorFlow (e.g., LSTM-based architectures) for:

- Univariate and multivariate GDP growth forecasting.
- Capturing temporal dependencies in economic indicators.

### MLflow

MLflow tracks:

- **Hyperparameters:** epochs, batch_size, learning rate, etc.  
- **Metrics:** loss, validation loss, MAE, MSE, R².  
- **Artifacts:** trained models, model architecture, plots, and logs.

This makes it easy to:

- Compare different model runs.
- Roll back to a known good model.
- Reproduce experiments reliably.

---

## Serving & Visualization

### FastAPI / Flask

Trained models can be exposed via REST APIs using FastAPI or Flask:

- `/predict` endpoint for GDP forecasts.
- Health check endpoints for monitoring.
- JSON-based request/response schema.

This enables integration with:

- Internal tools
- Dashboards
- External applications

### Streamlit

Streamlit apps can be used to:

- Explore data and feature distributions.
- Visualize forecasts and uncertainty bands.
- Provide interactive controls (country selection, forecast horizon, etc.).

### Looker

Looker (or a similar BI platform) can be integrated to deliver:

- Executive summary dashboards.
- Country/region-level forecast comparisons.
- Performance monitoring over time.

---

## Project Structure

> Note: Actual structure may vary slightly depending on implementation, but a typical layout is:

```text
World-Econ-Growth-Forecast/
├─ airflow/
│  ├─ dags/
│  │  └─ gdp_forecast_dag.py
│  └─ configs/
├─ data/
│  ├─ raw/
│  └─ processed/
├─ notebooks/
│  └─ exploration_and_eda.ipynb
├─ src/
│  ├─ data/
│  ├─ features/
│  ├─ models/
│  ├─ serving/
│  └─ utils/
├─ tests/
├─ assets/
│  ├─ Logo.jpg
│  ├─ DVC_in_GCP.png
│  ├─ airflow_dag.png
│  ├─ mlflow.PNG
│  └─ mlfloq1.PNG
├─ docker-compose.yaml
├─ Dockerfile
├─ dvc.yaml
├─ requirements.txt
└─ README.md
```

---

## Development Workflow

1. **Create a feature branch** from `dev` for new work.
2. **Develop and test** locally:
   - Update code in `src/`.
   - Add or update tests in `tests/`.
   - Run `pytest`.
3. **Log experiments** through MLflow:
   - Compare runs and pick candidate models.
4. **Commit DVC changes** for data/model updates:
   - `dvc add`, `dvc push`.
5. **Open a Pull Request** into `dev`:
   - GitHub Actions will run CI (pytest, lint, etc.).
6. **Merge to `main`** when validated:
   - Optional: Trigger deployment or container build.

---

## Roadmap

Potential future enhancements:

- Add richer macroeconomic and financial features (e.g., commodity prices, interest rates).
- Incorporate probabilistic forecasting (e.g., quantile or Bayesian models).
- Implement automated model selection and hyperparameter tuning.
- Add canary deployments or A/B tests for model updates.
- Extend the API to support scenario analysis (e.g., shocks to inflation or policy variables).

---

## License

This project is for educational and demonstration purposes.  
Please refer to the repository's LICENSE file (if present) for detailed licensing terms.
