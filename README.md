CI/CD Pipeline with Zero-Downtime Blue-Green Deployment
Problem
Modern applications require continuous delivery without impacting user experience. Traditional deployments introduce downtime because the existing service is stopped before the new version starts.

The problem addressed in this project:

Automate the build and deployment process

Eliminate downtime during releases

Ensure safe rollout of new versions

Securely manage deployment credentials

Enable quick rollback if failure occurs
Architecture
High-Level Flow

Developer pushes code to GitHub.

GitHub Actions triggers CI/CD pipeline.

Docker image is built for linux/amd64.

Image is pushed to Docker Hub.

GitHub Actions connects to EC2 via SSH.

New container (Green) is deployed.

Nginx switches traffic to new container.

Old container (Blue) is stopped and removed.
flowchart LR

Developer -->|Push Code| GitHub
GitHub -->|Trigger Workflow| GitHubActions
GitHubActions -->|Build Image| DockerHub
GitHubActions -->|SSH Deploy| EC2

User -->|HTTP Request| Nginx
Nginx -->|Proxy| BlueContainer
Nginx -->|Switch Traffic| GreenContainer
Design Decisions
1.Blue-Green Deployment Strategy

Chosen to:

Avoid downtime

Enable instant traffic switching

Reduce deployment risk

Instead of stopping the live container, a new container is started on a different port and traffic is switched only after successful startup.
2.Dockerized Application

Reasons:

Environment consistency

Easy portability

Simplified production deployment
3.GitHub Actions for CI/CD

Selected because:

Native GitHub integration

Easy secret management

No additional infrastructure required
4.Nginx as Reverse Proxy

Used to:

Route traffic dynamically

Switch between Blue and Green

Prepare for future HTTPS support
Failure Scenarios
Scenario 1: Docker Image Pull Failure

Cause: Image not available or wrong tag

Impact: Deployment stops

Mitigation: set -e in deploy.sh prevents unsafe switch

Scenario 2: New Container Fails to Start

Cause: Code crash, port conflict

Impact: Potential traffic issue

Mitigation: Nginx switch happens only after container runs

Scenario 3: EC2 SSH Failure

Cause: Invalid key or network issue

Impact: Deployment blocked

Mitigation: Secure SSH keys via GitHub Secrets

Scenario 4: Architecture Mismatch (arm64 vs amd64)

Cause: Built on Apple Silicon

Mitigation: Used docker buildx --platform linux/amd64
Monitoring (Current & Future Scope)
Current Monitoring

Manual container validation using:

docker ps

curl

docker logs

Nginx status verification
Future Improvements
Health check endpoint /health

Prometheus + Grafana integration

Auto rollback on health failure

Log aggregation

Cost Optimization

This project is designed with minimal infrastructure cost:

Single EC2 instance (no load balancer)

Docker-based deployment (no Kubernetes overhead)

GitHub Actions free tier

Open-source tooling (Nginx, Docker)

Estimated monthly cost:

EC2 t2.micro / t3.micro (Free Tier eligible)
Learnings

This project helped understand:

CI/CD pipeline automation

Blue-Green deployment mechanics

Zero-downtime release engineering

Secure secret management

Docker multi-architecture builds

Reverse proxy traffic switching

Infrastructure debugging in real cloud environment

It also exposed real-world issues such as:

Manifest architecture mismatch

Nginx misconfiguration

Inbound security rule setup

SSH key permission handling
