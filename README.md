# G11 Project Documentation

This repository contains comprehensive documentation for the G11 blog project, a microservices-based blog platform running on Kubernetes with CI/CD pipelines.

## Project Overview

The G11 project is a blog application backed by 5 microservices, deployed on Kubernetes with automated CI/CD pipelines including:
- Vulnerability scanning
- Rollout strategies with canary deployments
- Manual approval gate for production
- Automated infrastructure provisioning via Terraform
- Observability and monitoring

## Quick Links

### Core Documentation
- **[Architecture](ARCHITECTURE.md)** - System design, Kubernetes cluster, AWS infrastructure, and security measures
- **[Microservices](MICROSERVICES.md)** - Service overview, tech stack, and development environment
- **[CI/CD Pipeline](CICD_PIPELINE.md)** - GitHub Actions workflow and build process
- **[Deployment](DEPLOYMENT.md)** - ArgoCD, GitOps, and rollout strategies
- **[Kubernetes](KUBERNETES.md)** - K8s architecture, Helm charts, and GitOps patterns
- **[Infrastructure](INFRASTRUCTURE.md)** - AWS Terraform setup and scaling

### Getting Started
- **[Deployment Guide](DEPLOYMENT_GUIDE.md)** - Step-by-step deployment instructions

## Architecture Overview

### System Design
The platform consists of a Kubernetes cluster with Istio service mesh managing traffic between microservices. AWS infrastructure is provisioned via Terraform with auto-scaling capabilities. ArgoCD handles GitOps-based deployments with canary rollout strategies.

### Key Technologies
- **Container Orchestration**: Kubernetes with Istio
- **Infrastructure**: AWS (EC2, EFS, S3, Route 53)
- **Configuration Management**: Terraform, Helm
- **Deployment**: ArgoCD with canary rollouts
- **Security**: mTLS, sealed secrets, authorization policies
- **CI/CD**: GitHub Actions with vulnerability scanning

## Microservices

The platform comprises the following microservices:

| Service | Purpose | Repository |
|---------|---------|-----------|
| **user-service** | Authentication & user management | [g11-user-service](https://github.com/G11-Engineering/g11-user-service) |
| **category-service** | Blog categories & tags | [g11-category-service](https://github.com/G11-Engineering/g11-category-service) |
| **content-service** | Blog posts management | [g11-content-service](https://github.com/G11-Engineering/g11-content-service) |
| **comment-service** | Comment handling | [g11-comment-service](https://github.com/G11-Engineering/g11-comment-service) |
| **media-service** | Media/file management with AWS S3 | [g11-media-service](https://github.com/G11-Engineering/g11-media-service) |
| **frontend-service** | Next.js/React UI | [g11-frontend-service](https://github.com/G11-Engineering/g11-frontend-service) |

## Infrastructure & Deployment

| Component | Repository |
|-----------|-----------|
| **Helm Charts** | [g11-helm-charts](https://github.com/G11-Engineering/g11-helm-charts) |
| **Kubernetes Charts** | [g11-k8s-charts](https://github.com/G11-Engineering/g11-k8s-charts) |
| **Documentation** | [docs](https://github.com/G11-Engineering/docs) |
| **Workflows** | [.github](https://github.com/G11-Engineering/.github) |

## Security Features

- **Zero Trust Architecture**: mTLS enforcement between services
- **Secrets Management**: SealedSecrets for encrypted configuration
- **TLS/HTTPS**: Automatic certificate management with cert-manager
- **Authorization Policies**: Fine-grained service-to-service access control
- **Vulnerability Scanning**: Trivy scanning in CI/CD pipeline
- **Authentication**: Asgardeo OIDC integration with JIT provisioning

## Deployment Workflow

```
Code Change → GitHub Actions (Build & Test) 
  → Docker Image → GitOps Repo (Staging)
  → ArgoCD Sync → Canary Rollout (g11-stg)
  → PR Approval → Merge to Main (g11-prod)
  → ArgoCD Sync → Canary Rollout → Production
```

## Getting Help

- Check [Architecture](ARCHITECTURE.md) for system design questions
- See [Deployment Guide](DEPLOYMENT_GUIDE.md) for deployment steps
- Review [CI/CD Pipeline](CICD_PIPELINE.md) for build process details
- Explore individual repository READMEs for service-specific information

---

For more information about the G11 Engineering project, visit the [GitHub Organization](https://github.com/G11-Engineering)
