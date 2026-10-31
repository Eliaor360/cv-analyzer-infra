AI Job Screener | DevOps Infrastructure

Kubernetes • Jenkins CI/CD • Docker • Prometheus • Grafana

Infrastructure and deployment configuration for the AI Job Screener platform, an AI-powered recruitment application designed to automate CV analysis and candidate ranking.

This repository focuses on container orchestration, CI/CD automation, Kubernetes deployment, secrets management, and application monitoring.

Team Project: Developed collaboratively as part of a DevOps & DevSecOps final project.

Key Features

* Kubernetes Orchestration: Deployment of application components using Kubernetes manifests.
* CI/CD Automation: Jenkins pipeline for testing, building, publishing, and deploying application images.
* Containerization: Docker-based application packaging.
* Secrets Management: Kubernetes Secrets and Jenkins credentials for sensitive configuration.
* Monitoring & Observability: Prometheus metrics collection and Grafana dashboards.
* Automated Validation: Health checks and API smoke tests following deployment.
* Local Kubernetes Environment: Minikube-based deployment and testing.

Technology Stack

Category	Technologies
Containerization	Docker
Container Orchestration	Kubernetes, Minikube
CI/CD	Jenkins
Container Registry	Docker Hub
Monitoring	Prometheus, Grafana
Backend	Python, Flask
Frontend	React
Database	PostgreSQL
Configuration	Kubernetes ConfigMaps & Secrets
Testing	Unit Tests, API Smoke Tests

System Architecture

flowchart LR
    HR[HR User] --> FE[React Frontend]
    FE -->|REST API| BE[Flask Backend]
    BE --> PG[(PostgreSQL)]
    BE --> AI[AI Scoring Service]
    BE -->|Metrics| PROM[Prometheus]
    PROM --> GRAF[Grafana]

Architecture Components

Frontend: React-based interface for job management and candidate evaluation.

Backend: Flask REST API handling candidate processing, AI scoring, and analytics.

Database: PostgreSQL for persistent application data.

AI Integration: External AI scoring service with configurable API settings.

Monitoring: Prometheus collects application metrics, while Grafana provides visualization dashboards.

CI/CD Pipeline

The Jenkins pipeline automates the application delivery process.

Pipeline Stages

Checkout
   |
   v
Lint
   |
   v
Unit Tests
   |
   v
Docker Build
   |
   v
Push to Docker Hub
   |
   v
Deploy to Kubernetes
   |
   v
Health & API Tests

The pipeline is designed to validate application changes before deploying updated containers to Kubernetes.

Kubernetes Deployment

Prerequisites

* Docker
* Kubernetes
* kubectl
* Minikube

1. Start Minikube

minikube start --driver=docker
minikube addons enable ingress

2. Create Namespace

kubectl apply -f k8s/namespace.yaml

3. Configure Secrets

Never commit real credentials or API keys to Git.

kubectl -n job-screener create secret generic app-secrets \
  --from-literal=postgres-password=CHANGE_ME \
  --from-literal=muse-spark-key=CHANGE_ME \
  --from-literal=muse-spark-base-url=https://opencode.ai/zen/go/v1

Replace placeholder values with your own credentials.

4. Deploy Application

kubectl apply -n job-screener -R -f k8s/

Recommended deployment order:

1. Namespace
2. Secrets
3. ConfigMaps
4. PostgreSQL
5. Backend
6. Frontend
7. Ingress

5. Verify Deployment

kubectl -n job-screener get pods

Check backend rollout:

kubectl -n job-screener rollout status deploy/backend

6. Run Smoke Tests

bash scripts/smoke.sh http://$(minikube ip)

Alternatively, forward the backend service:

kubectl -n job-screener port-forward svc/backend 5000:5000

Then test using:

http://localhost:5000

For ingress-based access, configure screener.local to resolve to the Minikube IP address.

Jenkins Configuration

The pipeline uses Jenkins credentials to manage authentication and sensitive configuration.

Credential ID	Type	Purpose
docker-hub-creds	Username / Password	Docker Hub authentication
kube-config	Secret File	Kubernetes cluster access
muse-spark-key	Secret Text	AI API authentication
dockerhub-user	Environment Variable / Credential	Docker Hub username
muse-spark-base-url	Environment Variable / Credential	AI service endpoint

Credentials must be configured in Jenkins before running the pipeline.

Monitoring & Observability

The monitoring stack uses Prometheus and Grafana.

Deploy Prometheus

kubectl apply -n job-screener -f monitoring/prometheus.yaml

Application Metrics

The backend exposes metrics through:

GET /metrics

Example metric:

screening_requests_total

Grafana Dashboard

Import the dashboard configuration:

monitoring/grafana-dashboard.json

The dashboard includes:

* Application request rate
* AI processing failures
* P95 request latency

Candidate scoring analytics are also available through:

GET /api/analytics/overview

Security Considerations

The infrastructure includes several security-related practices:

* Sensitive configuration stored in Kubernetes Secrets.
* Jenkins credentials used for CI/CD authentication.
* Separation of application configuration from container images.
* Automated validation during the CI/CD process.
* Application health monitoring.

Note: Kubernetes Secrets are not encrypted by default in etcd unless encryption at rest is configured. Production deployments should also enforce appropriate RBAC and secret access controls.

Related Repository

Application Source Code:

AI Job Screener - Application

Project Credits

Developed collaboratively as part of a DevOps & DevSecOps final project.

This repository is maintained as a personal portfolio copy, preserving the original contribution history and acknowledging the collaborative development effort.
