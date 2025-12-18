# Architecture Diagrams

This directory contains all architecture diagrams used in the documentation.

## Diagram Files

Place the following PNG files in this directory:

### aws-infrastructure-diagram.png
- AWS infrastructure setup with Terraform
- Shows: Porkbun, Route 53, NLB, VPC, EC2, Auto Scaling, S3, IAM, EFS
- Referenced in: [INFRASTRUCTURE.md](../INFRASTRUCTURE.md)

### github-actions-pipeline.png
- GitHub Actions CI/CD workflow
- Shows: 5-stage pipeline (trigger, unit tests, security scans, build, push to Docker Hub)
- Services: frontend, content service, and others
- Referenced in: [CICD_PIPELINE.md](../CICD_PIPELINE.md)

### gitops-deployment-flow.png
- GitOps repository and deployment process
- Shows: Stage 6 (push to staging), Stage 7 (push to production)
- ArgoCD tracking branches (g11-prod, g11-stg)
- Referenced in: [DEPLOYMENT.md](../DEPLOYMENT.md)

### kubernetes-cluster-architecture.png
- Kubernetes cluster with Istio service mesh
- Shows: kube-system, istio-system, production/staging namespaces
- Components: Ingress Gateway, Pilot, Virtual Services, Postgres Operator, SealedSecrets
- Referenced in: [ARCHITECTURE.md](../ARCHITECTURE.md)
