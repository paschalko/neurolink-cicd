# Neurolink CI/CD Pipeline

Automated CI/CD pipeline for the Neurolink event networking platform,
built with Jenkins, Docker, and AWS EC2.

## Pipeline Overview

Every push to the main branch triggers an automated pipeline that:
1. Pulls the latest code from GitHub
2. Builds a Docker container image
3. Deploys the container with zero manual intervention
4. Runs a health check to verify the app is live

## Stack

- **CI/CD**: Jenkins
- **Containerization**: Docker + Docker Compose
- **Cloud**: AWS EC2
- **App**: Python/Flask

## Pipeline Stages
```
Checkout → Build → Deploy → Health Check
```

- **Checkout**: Jenkins pulls latest code from GitHub via SSH
- **Build**: Docker Compose builds the application image
- **Deploy**: Container is recreated and started automatically
- **Health Check**: curl verifies the app is responding before
  marking the build as successful

## What This Demonstrates

- Webhook-triggered CI/CD from GitHub
- Containerized deployment with Docker
- Automated pipeline orchestration via Jenkins
- Application health monitoring
- Secure credential management with Jenkins credentials store

## Architecture
```
GitHub (push) → Jenkins (poll/webhook) → Docker Build → 
Docker Deploy → Health Check → Live on AWS EC2
```

## Security

- SSH keys stored in Jenkins credentials store (never in code)
- Application repository kept private
- Docker socket access managed via group permissions
