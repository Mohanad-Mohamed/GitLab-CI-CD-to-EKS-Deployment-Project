#Architecture

<img width="1332" height="800" alt="Architecture" src="https://github.com/user-attachments/assets/912f1303-8df0-4331-b57c-938bf3e9dd5f" />

# GitLab CI/CD to EKS Deployment Project

A complete demonstration of deploying a Node.js application to Amazon EKS (Elastic Kubernetes Service) using GitLab CI/CD pipeline with Docker containerization.

## Project Overview

This project showcases a complete DevOps workflow:
- **Application**: Simple Node.js Express server with a web frontend
- **Containerization**: Docker image creation and management
- **CI/CD**: GitLab pipeline for automated building and deployment
- **Infrastructure**: Kubernetes deployment on AWS EKS
- **Registry**: GitLab Container Registry for image storage

## Project Structure

```
GitLab Project/
├── 07-2- Running the project locally .yaml       # Local setup instructions
├── 07-3- Create DockerFile .yaml                # Dockerfile configuration
├── 07-4- Building the project using GitLab .yaml # GitLab build setup
├── 07-5- Building Docker Images using Pipeline .yaml # Docker build pipeline
├── 07-6- Pushing Image to DockerHub .yaml        # DockerHub push configuration
├── 07-7- Versioning Docker image .yaml           # Image versioning strategy
├── 07-9- GitLab Container Registry .yaml         # GitLab registry setup
├── 08-2- Launch EC2 and Connect to EKS Cluster .yaml # EKS cluster setup
├── 08-4- Create GitLab Agent and Configure It .yaml   # GitLab agent configuration
├── 08-7- Create Docker Registry Secret in Kubernetes .yaml # K8s registry secret
├── 08-8- Create Kubernetes Deployment and Service .yaml # K8s manifests
└── 08-9- Deploy to EKS using GitLab CI-CD Pipeline .yaml # Main CI/CD pipeline
```

## Application Details

### Node.js Application
- **Framework**: Express.js
- **Port**: 3000
- **Features**: 
  - Serves static HTML frontend
  - REST API endpoint (`/api/image`) to display Docker image name
  - Environment variable support for image name

### Files
- `server.js`: Express server with API endpoint
- `package.json`: Node.js dependencies and scripts
- `public/index.html`: Frontend with gradient styling and image display
- `Dockerfile`: Multi-stage Docker build configuration
- `.gitlab-ci.yml`: GitLab CI/CD pipeline definition
- `deployment.yaml`: Kubernetes deployment manifest
- `service.yaml`: Kubernetes service manifest (LoadBalancer)

## Prerequisites

- Node.js (v22 or later)
- Docker
- GitLab account with Container Registry enabled
- AWS account with EKS cluster
- kubectl configured for EKS access
- GitLab Agent installed in Kubernetes cluster

## Local Development

### 1. Clone and Setup
```bash
git clone <repository-url>
cd gitlab-node-app
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Run Locally
```bash
npm start
```
Access the application at `http://localhost:3000`

## Docker Build

### Build Image
```bash
docker build -t gitlab-node-app:latest .
```

### Run Container
```bash
docker run -p 3000:3000 -e IMAGE_NAME=gitlab-node-app:latest gitlab-node-app:latest
```

## GitLab CI/CD Pipeline

### Pipeline Stages
1. **install**: Installs Node.js dependencies
2. **build_push_gitlab**: Builds Docker image and pushes to GitLab Container Registry
3. **deploy**: Deploys to EKS using kubectl

### Required Variables
Set these in GitLab CI/CD variables:
- `CI_REGISTRY_USER`: GitLab registry username
- `CI_REGISTRY_PASSWORD`: GitLab registry password (Personal Access Token)
- `KUBE_CONTEXT`: Kubernetes context name for EKS cluster

### Pipeline Execution
The pipeline automatically triggers on push to the repository:
- Builds Docker image with unique tag using `$CI_PIPELINE_IID`
- Pushes to GitLab Container Registry
- Updates Kubernetes deployment with new image
- Applies deployment and service manifests

## Kubernetes Deployment

### Prerequisites
1. Create Docker registry secret in Kubernetes:
```bash
kubectl create secret docker-registry gitlab-registry-secret \
  --docker-server=registry.gitlab.com \
  --docker-username=<username> \
  --docker-password=<password>
```

2. Configure GitLab Agent in your EKS cluster

### Deployment Manifests
- **deployment.yaml**: Defines 2 replicas with image pull secret
- **service.yaml**: LoadBalancer service with NLB type for internet-facing access

### Manual Deployment
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Architecture

```
GitLab Push → CI/CD Pipeline → Docker Build → GitLab Registry → EKS Deployment → LoadBalancer → Users
```

## Features

- **Automated CI/CD**: Complete pipeline from code to deployment
- **Image Versioning**: Unique image tags for each pipeline run
- **Kubernetes High Availability**: 2 replicas with LoadBalancer
- **GitLab Registry Integration**: Secure private container registry
- **Environment Variables**: Dynamic image name display in application
- **AWS NLB**: Network Load Balancer for optimal performance

## Troubleshooting

### Pipeline Failures
- Check GitLab CI/CD variables are correctly set
- Verify GitLab Container Registry permissions
- Ensure kubectl context is properly configured

### Deployment Issues
- Verify GitLab Agent is connected to EKS cluster
- Check Docker registry secret exists in Kubernetes
- Validate image pull permissions

### Access Issues
- Check LoadBalancer status: `kubectl get svc`
- Verify security groups allow traffic on port 80
- Check EKS cluster health
