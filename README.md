
# Project 3: Full Stack Java App on AWS EKS with CI/CD and Monitoring

This project demonstrates the deployment of a full-stack Java-based microservices application on **AWS EKS** using **Terraform**, **GitHub Actions**, and Kubernetes manifests. It also integrates **Prometheus** and **Grafana** for monitoring.

---

## 🧩 Stack Overview

- **Infrastructure as Code:** Terraform
- **Cloud Provider:** AWS (EKS, EC2, VPC, IAM)
- **Container Orchestration:** Kubernetes
- **CI/CD:** GitHub Actions
- **Monitoring:** Prometheus + Grafana
- **Application:** Java-based microservices (Spring Boot)

---

## Architecture Overview
![Project 1 architecture](images/architecture/project-3-architecture.png)

---
## 📦 Components

### Application Stack
- `app/` – Java backend app (Dockerized)
- `mysql/` – Custom MySQL image and Dockerfile

### Kubernetes Manifests
- `kubernetes/deployments/` – Deployment definitions
- `kubernetes/services/` – Service definitions (ClusterIP / LoadBalancer)
- `kubernetes/configmaps/` – Environment configuration
- `kubernetes/secrets/` – Application secrets
- `kubernetes/ingress/` – Ingress resource for routing

### Monitoring Stack
- `monitoring/prometheus/` – Prometheus deployment and config
- `monitoring/grafana/` – Grafana deployment and dashboards

### CI/CD
- `.github/workflows/` – GitHub Actions pipeline for:
  - Terraform Plan & Apply
  - Docker build & push
  - Kubernetes deploy

---

## 🚀 Project Goals

- Spin up AWS EKS infrastructure using Terraform
- Deploy app using Kubernetes manifests
- Automate deployment with GitHub Actions
- Set up real-time monitoring with Prometheus and Grafana
- Keep infrastructure modular and readable

---

## 📌 Notes

- No GitOps or Helm abstraction used — manifests are applied directly
- Focus is on delivering a **clean, working DevOps pipeline**
- Monitoring is a mandatory part of this project

---

## 🧠 What I Learned

_To be added after deployment & validation_

---

## ✅ Status

> Infrastructure: ⬜️  
> App Deployment: ⬜️  
> CI/CD Pipeline: ⬜️  
> Monitoring: ⬜️  
> Final Validation: ⬜️  
> Screenshots & Logs: ⬜️  

---

