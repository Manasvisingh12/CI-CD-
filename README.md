CI/CD Pipeline
Zero-Downtime Blue-Green Deployment on AWS
1. Overview

This repository demonstrates an end-to-end automated CI/CD pipeline implementing zero-downtime deployment using the Blue-Green strategy.

The system builds a Docker image, pushes it to Docker Hub, deploys to an AWS EC2 instance, and dynamically switches traffic using Nginx without interrupting active users.

The focus of this project is production-safe deployment automation.

Problem

Traditional deployment approaches introduce downtime by stopping the running service before starting a new version.

This project solves the following:

Eliminate downtime during deployments

Automate build and release workflows

Securely manage infrastructure credentials

Safely switch between application versions

Reduce deployment risk

Architecture
High-Level Flow

Developer pushes code to the main branch

GitHub Actions triggers CI/CD workflow

Docker image is built for linux/amd64

Image is pushed to Docker Hub

SSH connection established to EC2

New container is deployed

Nginx switches traffic

Old container is removed

Architecture Diagram
flowchart LR

Developer -->|Push Code| GitHub
GitHub -->|Trigger Workflow| GitHubActions
GitHubActions -->|Build Image| DockerHub
GitHubActions -->|SSH Deploy| EC2

User -->|HTTP Request| Nginx
Nginx -->|Proxy Traffic| BlueContainer
Nginx -->|Switch Traffic| GreenContainer

Technology Stack
Application Layer

Node.js

Containerization

Docker

Docker Hub

Infrastructure

AWS EC2 (Ubuntu)

Reverse Proxy

Nginx

CI/CD

GitHub Actions

CI/CD Workflow
Continuous Integration

Source checkout

Docker login

Multi-architecture image build

Image push to Docker Hub

Continuous Deployment

SSH to EC2

Execute deployment script

Start alternate container

Switch Nginx traffic

Remove old container

Deployment is automatically triggered on every push to main.

Blue-Green Deployment Strategy

Two application environments are maintained:

Blue Environment

Currently serving production traffic.

Green Environment

New application version being deployed.

Deployment sequence:

Detect active container

Start new container on alternate port

Validate startup

Update Nginx configuration

Stop previous container

This ensures uninterrupted service availability.

Design Decisions
Why Blue-Green Instead of Rolling Deployment?

Simpler to implement on a single EC2 instance

Instant traffic switching

Easier rollback

Why Nginx?

Lightweight reverse proxy

Simple port-based routing

Easy traffic switching

Why Docker?

Environment consistency

Easy portability

Simplified dependency management

Failure Scenarios
Docker Image Pull Failure

Deployment stops immediately using strict script error handling.

Container Startup Failure

Traffic is not switched until container runs successfully.

Architecture Mismatch (ARM vs AMD)

Resolved using docker buildx --platform linux/amd64.

SSH Authentication Failure

Handled securely using GitHub Actions secrets.

Monitoring and Validation

Current validation methods:

docker ps

curl

docker logs

nginx -t

Planned improvements:

Health check endpoint

Automated rollback logic

Metrics collection

Centralized logging

Cost Optimization

This architecture is intentionally lightweight:

Single EC2 instance

No load balancer

No orchestration layer

GitHub Actions free tier

Open-source tooling

Designed to operate within AWS Free Tier limits.

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

Local Setup

Build and run locally:

docker build -t myapp .
docker run -p 3000:3000 myapp


Access locally:

http://localhost:3000

Production Access

Application is accessible via:

http://<ec2-public-ip>

Key Learnings

CI/CD automation principles

Zero-downtime deployment strategies

Docker multi-architecture builds

Reverse proxy configuration

Secure secret handling

Infrastructure debugging in cloud environments

Conclusion

This project demonstrates a production-oriented CI/CD implementation using Blue-Green deployment with Docker, GitHub Actions, AWS EC2, and Nginx.

It reflects strong understanding of automation, deployment safety, and infrastructure integration.
