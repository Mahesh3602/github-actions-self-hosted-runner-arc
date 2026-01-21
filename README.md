# ☸️ GitHub Actions Runner Controller (ARC) on Kubernetes

## Introduction
This repository demonstrates the setup and deployment of **Actions Runner Controller (ARC)** on a Kubernetes cluster hosted on **AWS EC2**. It uses the modern **Autoscaling Runner Scale Sets** architecture to provide ephemeral, scalable runners for GitHub Actions.

### 💡 The Problem
Standard self-hosted runners often sit idle, wasting resources. Furthermore, they can suffer from "dirty" environments where files from previous builds interfere with new ones.

### 🛠️ The Solution
This repo implements **ARC (Actions Runner Controller)**, a Kubernetes-based solution that treats runners as ephemeral pods.

**Why use this setup?**
* **Auto-Scaling:** Pods are created on-demand when a workflow is triggered.
* **Ephemeral Environments:** Each job runs in a fresh container and is deleted immediately after completion—no manual cleanup required.
* **Resource Optimization:** Zero idle-time costs; your cluster only uses resources when your CI/CD is actually working.

## 🏗️ Architecture
- **Infrastructure:** EKS/AWS or standalone cluster with controlplane & workernode
- **Orchestration:** Kubernetes (K8s)
- **Controller:** Actions Runner Controller (ARC)
- **Scaling:** Runner Scale Sets (Autoscaling based on job demand)

## 🚀 Setup & Installation

### 1. Prerequisites
- A working Kubernetes cluster.
- Helm installed on the master node.
- A GitHub Personal Access Token (PAT) with `repo` scopes.


### 2. Environment Variables
export GITHUB_PAT="your_ghp_token"

export githubConfigUrl="[https://github.com/Mahesh3602/github-actions-self-hosted-runner-arc](https://github.com/Mahesh3602/github-actions-self-hosted-runner-arc)"


### 3. Install ARC Controller
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller

kubectl create namespace arc-systems

helm install arc-controller \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller \
    --namespace arc-systems

### 4. Install Runner Scale Set
helm install arc-runner-set \
    --namespace arc-runners \
    --create-namespace \
    --set githubConfigUrl="${githubConfigUrl}" \
    --set githubConfigSecret.github_token="${GITHUB_PAT}" \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set

### 5 . Verify- pods created and destroy during the job run
![Runner Pods](./Images/k8s-running-pods.png)
![Action Job](./Images/actions-job-logs.png)


