# CI-CD-jenkins-docker-kubernetes

Complete CI/CD pipeline with **Jenkins + Docker + Kubernetes (Minikube)** for automated application deployment.

## Requirements
- Jenkins **2.516.2**
- Java **17 (openjdk)**
- Docker
- Kubernetes
- Minikube

## Setup
1. Install Java 17 and Jenkins 2.516.2  
2. Install and start Docker, Kubernetes and Minikube:  
   `minikube start`  
3. Configure Jenkins **Multibranch Pipeline**: create a Multibranch Pipeline project in Jenkins, connect it to your **GitHub repository URL**, add **credentials** (Personal Access Token with `repo` permissions) so Jenkins can authenticate with GitHub and avoid API rate limiting. Jenkins will automatically scan branches with a `Jenkinsfile` and create build jobs per branch.

<img width="446" height="505" alt="312 drawio" src="https://github.com/user-attachments/assets/f33ffee5-ffa3-49dc-9805-37c6216d9e7c" />

## How It Works
Jenkins checks out your application and pipeline code from GitHub. It builds a **Docker image** using the `Dockerfile` stored in the repo. The image is pushed to **Docker Hub**. Jenkins updates the **Kubernetes manifest** with the new image tag. Jenkins then deploys the application to the local Kubernetes cluster running on **Minikube**. Finally, Minikube runs the containerized app and exposes it via a **NodePort service** for external access.
