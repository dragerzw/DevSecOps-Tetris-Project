# 🚀 Modern End-to-End DevSecOps Kubernetes Project 🌐

![DevSecOps](https://img.shields.io/badge/DevSecOps-Mastery-brightgreen)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Automation-blue)
![SonarCloud](https://img.shields.io/badge/SonarCloud-Code%20Quality-orange)
![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-9cf)
![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps%20CD-blue)
![Trivy & OWASP](https://img.shields.io/badge/Security-Trivy%20%7C%20OWASP-red)

Welcome to a fully automated DevSecOps learning experience! This project demonstrates deploying a containerized Tetris game onto an AWS Elastic Kubernetes Service (EKS) cluster completely automatically, managed purely by GitOps principles.

## 🏗️ Architecture Design

This repository features advanced, decoupled infrastructure running natively on GitHub Actions:

1. **Infrastructure as Code (Terraform CI):** Pushing changes to the `EKS-TF/` folder automatically provisions a fully isolated, production-ready Virtual Private Cloud (VPC), Internet Gateways, Subnets, and an EKS Cluster from scratch.
2. **Continuous Integration (GitHub Actions):** Pushing application code executes `cicd.yml`. This pipeline runs NPM dependencies, scans code quality via SonarCloud, checks open-source dependencies via OWASP, builds the Docker image, scans the filesystem and the image via Trivy, pushes the image to Docker Hub, and finally updates the Kubernetes manifest.
3. **Continuous Deployment (ArgoCD GitOps):** ArgoCD runs inside the AWS EKS Cluster continuously monitoring the `Manifest-file/` directory in this GitHub repository. Upon configuration updates, ArgoCD pulls the latest image tag and self-heals the application deployment autonomously.

## 📂 Repository Structure

- **`EKS-TF/`**: Advanced Terraform configurations that dynamically build the AWS Network layer and EKS control plane independently without relying on hardcoded lookup data.
- **`Manifest-file/`**: Kubernetes manifest files for the Tetris application and the ArgoCD declarative GitOps application (`argocd-tetris-app.yaml`).
- **`.github/workflows/`**: The dual-pipeline GitHub Actions definitions (`cicd.yml` for application sec and `terraform.yml` for infrastructure creation).
- **`Tetris-V2/`**: The NodeJS React-based Tetris game application code.

## 🚀 How to Run It Yourself

### 1. Configure GitHub Repository Secrets
To run these automated pipelines, you must add the following secrets to your repository under **Settings > Secrets and variables > Actions**:
* `AWS_ACCESS_KEY_ID`: IAM programmatic access key for deploying Terraform.
* `AWS_SECRET_ACCESS_KEY`: IAM secret key for deploying Terraform.
* `SONAR_TOKEN`: Token from your SonarCloud account for static code analysis.
* `DOCKER_USERNAME`: Docker Hub account username.
* `DOCKER_PASSWORD`: Docker Hub authentication token or password.
* `GH_PAT_TOKEN`: GitHub Personal Access Token (with `repo` and `workflow` scopes) allowing the CI pipeline to commit back the updated Docker tag to `Manifest-file`.

### 2. Scaffold the Infrastructure
Make any push to the `EKS-TF/` folder, or trigger the `Terraform EKS Deployment` workflow manually from the Actions tab. Wait ~15 minutes for the AWS VPC and EKS Cluster to spin up natively.

### 3. Build the Application
Make any push to `Tetris-V2/` (or manually trigger the `Tetris DevSecOps Pipeline`). The CI/CD engine will scan security, build the image, and update the Kubernetes deploy manifest files on the `main` branch.

### 4. Deploy ArgoCD (GitOps)
Connect to your new cluster locally and install ArgoCD in its namespace. Run the declarative app:
```bash
aws eks update-kubeconfig --region us-east-1 --name Tetris-EKS-Cluster
kubectl create namespace argocd
kubectl apply --server-side --force-conflicts -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f Manifest-file/argocd-tetris-app.yaml
```

ArgoCD will automatically take over syncing the cluster state to match the GitHub `main` branch perfectly!

## 📄 License
This portfolio project is licensed under the Apache-2.0 license.