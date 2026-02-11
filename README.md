CI/CD Pipeline with Zero-Downtime Blue-Green Deployment
Overview

This project demonstrates an end-to-end automated CI/CD pipeline implementing zero-downtime deployment using the Blue-Green strategy.

The pipeline builds a Docker image, pushes it to Docker Hub, deploys it to an AWS EC2 instance, and switches traffic via Nginx without interrupting active users.

The objective is to simulate a production-grade deployment workflow focusing on release safety, automation, and infrastructure reliability.

Problem Statement

Traditional deployment strategies introduce downtime by stopping the currently running application before starting the new version.

This project addresses the following challenges:

Automating build and deployment workflows

Eliminating service downtime during releases

Safely switching between application versions

Managing secrets securely

Handling common infrastructure-level failure scenarios

Technology Stack

Node.js

Docker

Docker Hub

AWS EC2 (Ubuntu)

Nginx (Reverse Proxy)

GitHub Actions

Blue-Green Deployment Strategy

Architecture
High-Level Flow

Developer pushes code to the main branch.

GitHub Actions triggers the CI/CD workflow.

Docker image is built for linux/amd64 architecture.

Image is pushed to Docker Hub.

GitHub Actions connects to EC2 via SSH.

A new container (Green) is started on a separate port.

Nginx switches traffic to the new container.

The old container (Blue) is stopped and removed.

Architecture Diagram
flowchart LR

Developer -->|Push Code| GitHub
GitHub -->|Trigger Workflow| GitHubActions
GitHubActions -->|Build Docker Image| DockerHub
GitHubActions -->|SSH Deploy| EC2

User -->|HTTP Request| Nginx
Nginx -->|Reverse Proxy| BlueContainer
Nginx -->|Switch Traffic| GreenContainer

CI/CD Workflow

The CI/CD pipeline is defined using GitHub Actions.

Workflow Steps

Checkout source code

Authenticate with Docker Hub

Build Docker image for linux/amd64

Push image to Docker Hub

SSH into EC2 instance

Execute deployment script

Start new container

Switch Nginx traffic

Remove old container

Deployment is automated and triggered on every push to the main branch.

Blue-Green Deployment Strategy

Two application environments are maintained:

Blue (currently live)

Green (new version)

The deployment process:

Detect currently active container.

Start new container on alternate port.

Wait for container readiness.

Switch Nginx proxy configuration.

Stop and remove old container.

This ensures continuous availability during deployment.

Secrets Management

Sensitive credentials are stored securely using GitHub Actions Secrets:

DOCKER_USERNAME

DOCKER_PASSWORD

EC2_HOST

EC2_USER

EC2_KEY

No credentials are committed to the repository.

Project Structure
.
├── Dockerfile
├── deploy.sh
├── index.js
├── package.json
├── .github/
│   └── workflows/
│       └── deploy.yml
└── README.md

Deployment Script Logic

The deployment script performs:

Image pull from Docker Hub

Live container detection

Alternate container startup

Nginx port switching

Old container cleanup

The script uses set -e to prevent unsafe deployments in case of failure.

Failure Scenarios Considered
Docker Image Pull Failure

Mitigation: Deployment stops immediately due to strict error handling.

Container Startup Failure

Mitigation: Traffic switch occurs only after container startup.

Architecture Mismatch (arm64 vs amd64)

Mitigation: Docker image built using buildx with explicit linux/amd64 platform.

SSH Authentication Failure

Mitigation: Secure key handling via GitHub Secrets.

Monitoring and Validation

Current validation approach:

docker ps for container verification

curl for application response testing

docker logs for debugging

nginx -t for configuration validation

Future improvements may include:

Health check endpoint

Automated rollback

Prometheus and Grafana integration

Centralized logging

Cost Considerations

The architecture is intentionally minimal to reduce operational cost:

Single EC2 instance

No load balancer

No container orchestration layer

GitHub Actions free tier

Open-source reverse proxy

Designed to run within AWS Free Tier limits.

How to Run Locally

Build and run locally using Docker:

docker build -t myapp .
docker run -p 3000:3000 myapp


Access locally at:

http://localhost:3000

Production Access

Application is served via Nginx on:

http://<ec2-public-ip>

Key Learnings

Implementing automated CI/CD pipelines

Designing zero-downtime deployment strategies

Managing secrets securely in CI environments

Handling Docker multi-architecture builds

Configuring reverse proxy routing

Debugging infrastructure-level issues in cloud environments

Conclusion

This project demonstrates a production-oriented CI/CD workflow implementing Blue-Green zero-downtime deployment using Docker, GitHub Actions, AWS EC2, and Nginx.

It reflects a strong understanding of automation, release engineering, infrastructure configuration, and deployment safety principles.
