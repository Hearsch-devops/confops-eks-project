
# ConfOps EKS Project

A cloud-native Conference Room Booking application deployed on Amazon EKS using Kubernetes, Jenkins CI/CD, Docker, and Amazon ECR.

---
# Project Overview
This project demonstrates an end-to-end DevOps workflow for deploying a containerized Flask application on Kubernetes (EKS) with automated CI/CD pipelines and persistent PostgreSQL storage.

The application allows users to:
- Book conference rooms
- View available rooms
- Calculate pricing dynamically
- Store booking data in PostgreSQL
- Deploy updates automatically using Jenkins
---

---
## CI/CD Workflow
GitLab → Jenkins → Docker Build → Amazon ECR → Amazon EKS → Kubernetes Pods
---

---
## Infrastructure Components
- Amazon EKS (Kubernetes Cluster)
- Amazon ECR (Container Registry)
- Jenkins CI/CD Pipeline
- Flask Backend
- PostgreSQL Database
- Kubernetes Deployments & Services
- Persistent Volumes using EBS CSI Driver
- Infrastructure Provisioning using eksctl + AWS CloudFormation
---

---
# Tech Stack

|        Category             |      Technology         |
|          ---                |         ---             |
| Cloud                       | AWS                     |
| Infrastructure Provisioning | eksctl + CloudFormation |
| Containerization            | Docker                  |
| Orchestration               | Kubernetes (EKS)        |
| CI/CD                       | Jenkins                 |
| Source Control              | GitLab                  |
| Container Registry          | Amazon ECR              |
| Backend                     | Flask (Python)          |
| Database                    | PostgreSQL              |
| Storage                     | AWS EBS CSI Driver      |
---
---
# Features
- Automated CI/CD pipeline
- Versioned Docker image deployments
- Kubernetes-based deployment
- Persistent PostgreSQL storage
- Dynamic room pricing
- REST API integration
- Cloud-native architecture
- Automated infrastructure provisioning using eksctl
---

Docker Workflow:
Build Docker Image
docker build -t confops:${BUILD_NUMBER} .

Push Image to ECR
docker push <ECR_URI>:${BUILD_NUMBER}

Kubernetes Deployment:
Apply Kubernetes Resources
kubectl apply -f kubernetes/
Verify Pods
kubectl get pods -n conference-room

EKS Cluster Creation
The Kubernetes cluster was provisioned using eksctl, which internally uses AWS CloudFormation to create and manage AWS infrastructure resources.
Create EKS Cluster
eksctl create cluster \
  --name confops-cluster \
  --region us-east-1 \
  --nodegroup-name workers \
  --node-type t3.small \
  --nodes 2 \
  --managed

Jenkins CI/CD Pipeline
Pipeline stages include:
Clone Source Code
Build Docker Image
Push Image to Amazon ECR
Deploy to Amazon EKS
Rollout Verification

Screenshots
Path: AWS_EKS_ECR_Project.pdf

Key Learnings
Kubernetes Operations on AWS EKS
CI/CD Automation with Jenkins
Docker Image Management using ECR
Persistent Storage using EBS CSI Driver
AWS Infrastructure Provisioning using eksctl
Troubleshooting Kubernetes Scheduling & Storage Issues

Future Improvements
Ingress Controller
AWS Load Balancer Controller (ALB)
Route53 Integration
HTTPS with Cert-Manager
Helm Charts
Monitoring with Prometheus & Grafana
