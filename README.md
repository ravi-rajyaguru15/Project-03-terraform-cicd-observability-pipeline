
# Project 3: EKS Infrastructure Deployment with Terraform, GitHub Actions CI/CD, and Observability  

[![Infrastructure](https://img.shields.io/badge/IaC-Terraform-blue)](#)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub_Actions-green)](#)
[![Monitoring](https://img.shields.io/badge/Observability-Grafana%20%26%20Prometheus-orange)](#)
[![Status](https://img.shields.io/badge/Deployment-Complete-brightgreen)](#)


---

##  Part of a 3-Project DevOps Progression

This project is the **final milestone** in a self-designed 3-part DevOps portfolio projects designed to mirror the progression of infrastructure maturity in real-world engineering environments — from containerization, to orchestration, to full automation and observability on real world cloud infrastructure.

- **Project 1**: Multi-service containerization and deployment using Docker Compose on AWS EC2 - [Project 1](https://github.com/ravi-rajyaguru15/Project-01-dockerized-multi-service-application)
- **Project 2**: Kubernetes orchestration of the same application stack on AWS EKS - [Project 2](https://github.com/ravi-rajyaguru15/Project-02-eks-k8s-infrastructure-orchestration)
- **Project 3 (this)**: Infrastructure-as-Code with Terraform, CI/CD via GitHub Actions, and monitoring using Prometheus and Grafana

---

## Project Overview

This project is the culmination of a three-stage DevOps transformation system, integrating all foundational elements of modern cloud infrastructure, automation, and operations. It takes the multi-service web application containerized and orchestrated in earlier projects, and builds a production-grade platform around it — fully automated, reproducible, and observable.

All infrastructure provisioning is handled through Terraform, which defines and deploys the EKS cluster, VPC, subnets, IAM roles, and load balancers. On top of that, GitHub Actions orchestrates an end-to-end CI/CD pipeline that builds, tests, and deploys Kubernetes resources to the live cluster with zero manual intervention. Kubernetes objects needs to be deployed manually for the first time just like Project 2, after that GitHub Actions can take over any further changes.

For deployment, the application components (Java web app, MySQL, RabbitMQ, Memcached) are deployed into EKS via modular YAML manifests, and exposed to the internet via an Ingress controller integrated with AWS ELB.

Finally, Prometheus and Grafana are deployed to provide real-time observability into application health, pod performance, and system-level metrics — completing the loop from infrastructure to operations.

This system mirrors how a DevOps engineer would structure and automate real-world application delivery at scale, combining provisioning, deployment, and monitoring in a clean, modular, GitOps-friendly setup.

---

##  Application Stack

| Layer      | Component        | Purpose                                  |
|------------|------------------|------------------------------------------|
| Web        | `web-app`        | Java web app via Tomcat (+ Maven build)  |
| Database   | `mysql`          | PVC-backed relational Database           |
| Messaging  | `rabbitmq`       | Queue-based messaging                    |
| Caching    | `memcached`      | In-memory caching layer                  |
| Ingress    | `nginx`          | HTTP routing & reverse proxy             |

---

##  Infrastructure Overview (AWS)

- EKS cluster provisioned via Terraform with managed node groups
- VPC with public and private subnets, route tables, NAT gateway, and security groups (Terraform-managed)
- Elastic Block Store (EBS) for persistent volume claims (MySQL storage)
- IAM role + EBS CSI Driver configured via Terraform for dynamic PVC provisioning
- Application load balancing handled by AWS ELB via NGINX Ingress annotations
- S3 bucket configured as Terraform backend to store remote state
- DynamoDB table used to manage Terraform state locking and consistency

---

## Architecture Overview

![Project 3 architecture](images/architecture/project-3-architecture.png)

---

## Repository Structure
```text
Project_03/
│
├── .github/                                       # GitHub Actions CI/CD pipeline
│   └── workflows/                                
│       └── deploy.yaml
│
├── terraform/                                     # Modular Terraform setup for AWS EKS
│   ├── aws-ebs-csi.tf                             # EBS CSI driver IAM setup
│   ├── eks-cluster.tf                             # EKS control plane config
│   ├── main.tf                                    # Terraform provider & backend
│   ├── outputs.tf                                 # Generates info like cluster endpoint after infrastucture is provisioned
│   ├── terraform.tf                               # Terraform Dependencies
│   ├── variables.tf                               # Variables for AWS region and cluster name
│   └── vpc.tf                                     # VPC, subnets, NAT, etc.
│            
├── kubernetes/
│   ├── deployments/                               # Pods for app, mysql, rabbitmq, memcached 
│   │   ├── app-deployment.yaml                      and initContainers
│   │   ├── mysql-deployment.yaml
│   │   ├── rabbitmq-deployment.yaml
│   │   └── memcached-deployment.yaml
│   │
│   ├── services/                                  # ClusterIP services      
│   │   ├── web-app-service.yaml
│   │   ├── mysql-service.yaml
│   │   ├── rabbitmq-service.yaml
│   │   └── memcached-service.yaml
│   │
│   ├── ingress/                                   # NGINX ingress resource
│   │   └── nginx-ingress.yaml
│   │
│   ├── secrets/                                   # Base64 encoded secrets containing MySQL 
│   │   └── secrets.yaml                             and RabbitMQ credentials
│   │
│   └── persistentVolumeClaim/                     # MySQL PVC
│       └── mysql-pvc.yaml
│      
├── images/
|   ├── screenshots/...                            # Screenshots of k8s pods & services, terraform apply & plan, CI/CD pipeline, Grafana monitoring, etc.
│   └── architecture/
|         └── project-3-architecture.png           # Architecture overview diagram of Project 3
│      
├── legacy/                                        # Files and artifacts carried over from project 1 and 2  
│   ├── app/...                                      for continuity purposes, but are not an active  
│   ├── mysql/...                                    part of project 3.
│   ├── nginx/...
│   ├── images/...                         
│   ├── .env                                                                           
│   ├──docker-compose.yaml
│   └──project2-eksctl-config.yaml 
│ 
├── .gitignore                                     # gitignore file to prevent large runtime generated files from pushing into repository.                     
│                     
└── README.md                                      # Project README (You are here) (Inception!)
```
---

## How to Deploy (Reproduction steps)

1. **Prerequisites/Dependencies**:
      - Local machine with `aws-cli`, `eksctl`, `kubectl`, and `git` installed
      - AWS account configured via aws cli, and with appropriate IAM user, and access rights
      - Terraform installed and accessible globally
      - GitHub repository created to store the project, and configured with appropriate Actions Repository secrets to access your AWS account.  
      - A remote S3 bucket created manually beforehand (for storing Terraform backend state) with matching name in terraform/terraform.tf file.

2. Open the bash terminal and fetch the project files via git clone, and navigate into the project folder.

3. Create EKS cluster via terraform: Make sure you are into Project-3/terraform/ before executing these commands.

    ```bash
    terraform init
    terraform plan
    terraform apply     # Approve this request with "yes" when Terraform prompt occurs
    ```
4. Connect to the cluster and get node information: Make sure you are into Project-3/ before executing these commands.

    ```bash
    aws eks update-kubeconfig --region us-east-1 --name project3-eks-cluster
    ```
    ```bash
    kubectl get nodes
    ```
5. Install NGINX Ingress Controller and verify its pods and services:

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/aws/deploy.yaml
    ```
    ```bash
    kubectl get pods -n ingress-nginx
    kubectl get svc -n ingress-nginx
    ```
6. Apply Kubernetes Manifests:

    ```bash
    kubectl apply -f kubernetes/secrets/
    kubectl apply -f kubernetes/persistentVolumeClaim/
    kubectl apply -f kubernetes/deployments/
    kubectl apply -f kubernetes/services/
    kubectl apply -f kubernetes/ingress/
    ```
7. Verify Deployment:

    ```bash
   kubectl get pods
   kubectl get pvc
   kubectl get all
    ```
8. Access the web-application using the DNS endpoint from ingress nginx service: 

    ```bash
    # Locate EXTERNAL-IP of ingress-nginx service
    
    kubectl get svc -n ingress-nginx
    ```
9. CI/CD integration: Push changes to GitHub to trigger the CI/CD pipeline. 

     ```bash
     git add .
     git commit -m "CI/CD deploy update"
     git push origin main
     ```
10. Monitoring setup with Helm:
    - Add Prometheus and Grafana charts: 
     
     ```bash
     helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
     helm repo add grafana https://grafana.github.io/helm-charts
     helm repo update
     ```
    - Create a namespace for monitoring:
     
     ```bash
     kubectl create namespace monitoring
     ```
    - Install Prometheus:
     
     ```bash
     helm install prometheus prometheus-community/prometheus \
     --namespace monitoring \
     --create-namespace \
     --set server.persistentVolume.enabled=false
     ```
     - Install Grafana:
     
     ```bash
     helm install grafana grafana/grafana --namespace monitoring
     ```
     - get Grafana password and access the grafana GUI via port forwarding:
     
     ```bash
     kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
     kubectl port-forward --namespace monitoring svc/grafana 3000:80
     
     # Open http://localhost:3000 
     # Default credentials: admin / <decoded-password>
     ```
11. Monitoring: Import a Prebuilt Dashboard into Grafana (step-by-step)
     - Log in to Grafana
     - Add prometheus as a Data source:
          - On Grafana home page, on left sidebar select "Connections -> Data Sources"
          - Once inside Data sources, select "Add data source -> Select Prometheus"
          - Once inside Prometheus setup, set URL to "http://prometheus-server.monitoring.svc.cluster.local" -> Save & Test
     - Import a Kubernetes monitoring dashboard:
          - On Grafana home page, on left sidebar select "Dashboards -> New -> Import"
          - Input the dashboard ID (see list below) -> select "Load"  
          - In next step select Prometheus as data source. The monitoring window will now be live.
     - Recommended Dashboard IDs:
           
            | Dashboard                                         | Grafana.com ID   | 
            |---------------------------------------------------|------------------|
            | Kubernetes cluster monitoring (via Prometheus)    |       315        |
            | Node Exporter Full (node-level metrics)           |       1860       | 
            | Prometheus 2.0 Overview                           |       3662       | 
12. After verifying everything, terminate and clean everything to avoid incurring unnecessary AWS costs:
    Make sure you are into Project-3/terraform/ before executing these commands.
    
    ```bash
     # Destroy the EKS module only, which removes the cluster, node groups, load balancers (ELB/NLBs) and their Elastic Network Interfaces (ENI)
     terraform destroy -target=module.eks -auto-approve

     # With the ENIs gone, Terraform can now delete subnets and detach the Internet Gateways (IGW).
     terraform destroy -auto-approve

     #Summary: First remove the EKS layer to free its load-balancer network interfaces; then the full destroy can safely delete the remaining subnets and Internet Gateway.
     ```
```text
Notes on terraform destroy:

In some cases, `terraform destroy` may fail due to AWS retaining Elastic IPs, NAT Gateways, or ENIs tied to the VPC or subnets. If that happens:

- Manually delete these resources via AWS CLI or Console
- Then retry `terraform destroy`

This is a known AWS behavior and not a Terraform configuration error.
```
---

## Engineering Insights

- **Terraform-Only Infrastructure**: No manual AWS setup. Every component is provisioned via Terraform — including VPC, subnets, NAT, internet gateway, EKS cluster, worker nodes, and IAM policies.
- **GitHub Actions CI/CD Pipeline**:
     - Pipeline split into 4 separate jobs: Terraform Plan → Terraform Apply → Kubernetes Apply → Verify Deployment.
     - Secret management is handled securely via GitHub Secrets.
     - The pipeline ensures a zero-touch deployment after pushing to main.
- **EBS CSI Driver via Terraform**:
     - Required for MySQL persistent storage.
     - IAM role is programmatically attached using an additional Terraform file.
- **Kubernetes Infrastructure**:
     - Manifests are modular: secrets, services, PVC, deployments, ingress.
     - Manifests are written manually and taken over from Project 2 - ensuring reusability and reducing redundant effort.
     - initContainers used for readiness sequencing (e.g., MySQL PVC needs cleaning before mount).
- **Observability with Prometheus & Grafana**:
     - Monitoring is enabled through Prometheus scraping.
     - Grafana dashboards are used for pod, node, and app health.
     - Screenshots included as visual proof of observability.
- **Debugging Real-World Issues**: Solved a number of realistic issues like:
     - IAM bindings for EBS CSI - automated it instead of performing manual patchwork everytime
     - CrashLoopBackOff due to initContainer misconfiguration, and PVC mounting error.
     - Prometheus service pod not initializing due to Persistent Volume not binding - solved via disabling PV ensuring quick troubleshooting deployment.
- **Docker Hub registry images rebuilt**:  Custom web app changes were not reflected in previous Docker image — rebuilt and pushed custom app image to Docker Hub again to preserve integrity.

---

## End-to-End Workflow

Here's what happens when a commit pushed to main branch, after initial setup:

- GitHub Actions Triggered:
     - All secrets and configs passed via env and GitHub secrets.

- Terraform Plan + Apply:
     - Creates complete AWS infrastructure.
     - Handles state locking and backend storage.

- Kubernetes Apply:
     - Deploys app, services, ingress, PVC, secrets into EKS.

- Validation Step:
     - Pipeline checks pod health and ingress reachability using kubectl.

---

## What This Project Project Demonstrates

- End-to-end automation: Provisioned AWS infrastructure entirely via Terraform, including EKS, IAM, VPC, and networking layers — no manual AWS setup.
- CI/CD pipeline ownership: Built a GitHub Actions pipeline for zero-touch infrastructure provisioning and app deployment.
- Kubernetes orchestration at scale: Deployed and debugged Secrets, PVCs, initContainers, and Ingress in a multi-service setup.
- Production-grade monitoring: Enabled full-stack observability using Prometheus and Grafana (via Helm) with working dashboards.
- Practical DevOps problem-solving: Independently solved real-world issues around IAM bindings, storage drivers, init sequencing, and crash loops.
- Clean, modular system architecture: Separated IaC, app manifests, secrets, CI, and legacy layers for maintainability and reuse.

---

## Final Thoughts

This project was not only a demonstration of building end-to-end infrastructure — it was a test of full-cycle ownership, system design, and debugging under realistic constraints. Every component in this system, from VPC routing to CI automation to monitoring dashboards, was individually understood, implemented, and validated.

Rather than relying on out-of-the-box scaffolding or third-party abstractions, all infrastructure was written independently using Terraform, and every Kubernetes resource was deployed and debugged directly through version-controlled manifests. This includes realistic challenges such as IAM policy attachment, storage driver configuration, initContainer sequencing, and load balancer integration — each of which required non-trivial debugging and architectural decisions.

The project also demonstrates alignment with real-world expectations for delivery automation and systems observability. The CI/CD pipeline in GitHub Actions ensures deployment repeatability with minimal manual steps, while Prometheus and Grafana provide insight into both application health and platform stability.

In summary, this repository reflects a complete, deployable system — not a sample project. It encapsulates infrastructure-as-code, pipeline automation, and runtime observability in a way that is modular, maintainable, and ready for extension in team-scale environments.

---

## Attribution
The Java web application used in this project was externally sourced. All containerization, orchestration, deployment strategy, and infrastructure setup were independently implemented. Although it being way out of scope, and to ensure the system appeared more production-ready and portfolio-appropriate, all course-specific branding was removed; UI elements and presentation were modified to reflect a generic, open-source-style web application. 

A deliberate and calculated decision was also made to use the same web application throughout all 3 projects. This allowed full attention to be directed towards infrastructure, deployment, and DevOps process engineering.

---