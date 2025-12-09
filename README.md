# OPS410 Project 2 – Azure Kubernetes Service (AKS) Deployment with Autoscaling

## Project Overview

This project demonstrates a full cloud-native deployment of the Azure Voting App using Azure Kubernetes Service (AKS) and Azure Container Registry (ACR). The objective was to deploy a two-tier application, expose it publicly, and implement Horizontal Pod Autoscaling (HPA) to automatically scale the front-end application based on CPU demand.

The solution consists of:
- Redis backend (azure-vote-back) for storing votes
- Front-end web application built into a container image and stored in ACR
- Kubernetes Deployments, Services, and Autoscaler running on AKS
- Load testing to trigger autoscaling behavior

This project follows real DevOps and cloud architecture principles similar to production environments.

## Technologies Used

- Microsoft Azure
- Azure Kubernetes Service (AKS)
- Azure Container Registry (ACR)
- Azure Cloud Shell
- Docker / ACR Task Image Builds
- Kubernetes (Deployments, Services, HPA)
- Redis
- Load testing with hey

## Repository Structure

OPS410-Project2-AKS-Autoscaling/
├── README.md
├── k8s/
│   ├── azure-vote-back.yaml
│   ├── service-back.yaml
│   ├── azure-vote-front.yaml
│   ├── service-front.yaml
│   └── hpa.yaml
└── docs/
    └── demo-commands.md

## Architecture Summary

A standard AKS deployment model was used:

- ACR stores the custom-built front-end image
- AKS cluster runs both the backend and front-end pods
- ClusterIP service exposes Redis internally
- LoadBalancer service exposes the front-end publicly
- Horizontal Pod Autoscaler monitors CPU and adjusts replica count
- Hey load tester simulates traffic to trigger scaling

This architecture demonstrates both container orchestration and elastic scaling.

## Deployment Workflow (Summary)

### 1. Build & Push the Front-End Image to ACR
az acr build --registry ops410prj02oogundipe3 --image azure-vote-front:v1 .

### 2. Deploy the Redis Backend
kubectl apply -f k8s/azure-vote-back.yaml
kubectl apply -f k8s/service-back.yaml

### 3. Deploy the Front-End Application
kubectl apply -f k8s/azure-vote-front.yaml
kubectl apply -f k8s/service-front.yaml

Retrieve the external IP:
kubectl get svc azure-vote-front

Open the app in a browser:
http://<external-ip>

### 4. Enable Horizontal Pod Autoscaling
kubectl apply -f k8s/hpa.yaml
kubectl get hpa

### 5. Run Load Testing to Trigger Autoscaling

Make the load tester executable:
chmod +x hey_linux_amd64

Run a 1-minute test with 50 concurrent connections:
./hey_linux_amd64 -z 1m -c 50 http://<external-ip>/

Watch autoscaling in real time:
kubectl get pods -w
kubectl get hpa -w

As CPU increases, Kubernetes automatically creates more replicas.  
After load ends, the number of pods returns to the minimum.

## Autoscaling Behavior

During testing, the following occurred:

- CPU usage rose above the HPA threshold (50%)
- HPA increased the number of front-end pod replicas
- More pods were created to handle incoming traffic
- After the load test finished, the replicas scaled back down

This demonstrated real-time elasticity and efficient resource usage.

## Key Learning Outcomes

Through this project, I gained practical experience with:

- Deploying containerized applications on AKS
- Building and pushing images to Azure Container Registry
- Writing and applying Kubernetes YAML manifests
- Understanding Kubernetes networking (ClusterIP + LoadBalancer)
- Configuring and observing Horizontal Pod Autoscaling
- Running load tests and analyzing system performance
- Managing cloud resources in a real deployment workflow

This project reinforced essential cloud and DevOps skills used in real-world environments.

## Author

Oluwadamilola “Dammy” Ogundipe
Honours Bachelor of Information Technology – Design & Management
Seneca Polytechnic (OPS410)
