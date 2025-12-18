# Deployment Guide

## Overview
This guide provides step-by-step instructions for deploying the G11 blog platform using Terraform, Kubernetes, and GitOps.

## Prerequisites
- AWS Account with appropriate permissions
- Kubernetes cluster (v1.20+)
- ArgoCD installed in cluster
- Terraform for infrastructure provisioning
- kubectl configured for cluster access
- Docker for image building (for development)

## Infrastructure Setup

### Step 1: Provision AWS Infrastructure with Terraform
1. Configure AWS credentials
2. Set up Terraform variables for your environment
3. Run `terraform init` to initialize Terraform
4. Execute `terraform plan` to review changes
5. Apply with `terraform apply` to provision:
   - VPC and networking (NLB, Internet Gateway)
   - EC2 instances (Master node and worker nodes)
   - Auto Scaling groups for worker nodes
   - S3 buckets for Terraform state and certificates
   - Route 53 DNS configuration
   - IAM roles and policies
   - EFS storage

### Step 2: Bootstrap Kubernetes Cluster
1. Verify master and worker nodes are running
2. Configure kubectl to access the cluster
3. Install system components:
   - CNI (Container Network Interface)
   - kubelet and kube-proxy on worker nodes
   - cluster-autoscaler for node scaling

## Kubernetes Configuration

### Step 3: Install System Components
1. Install Istio service mesh:
   ```
   istioctl install --set profile=production
   ```
2. Install cert-manager for TLS certificates
3. Install SealedSecrets for secret management
4. Install AWS EFS CSI Driver for storage
5. Install metrics-server for resource monitoring

### Step 4: Install ArgoCD
1. Create argocd namespace: `kubectl create namespace argocd`
2. Install ArgoCD: `kubectl apply -f argocd/install.yaml`
3. Configure ArgoCD to track GitOps repositories:
   - Production repo (main branch) → g11-prod cluster
   - Staging repo (staging branch) → g11-stg cluster

### Step 5: Create Namespaces
1. Create g11-prod namespace for production
2. Create g11-stg namespace for staging
3. Enable Istio injection: `kubectl label namespace g11-prod istio-injection=enabled`
4. Configure network policies and authorization policies

## Deployment Process

### Step 6: Configure Secrets
1. Create Asgardeo credentials as SealedSecrets
2. Database credentials encrypted and stored
3. AWS S3 credentials for media service
4. TLS certificates via cert-manager

### Step 7: Deploy via GitOps
1. Push application manifests to GitOps repository (staging branch)
2. ArgoCD detects changes in staging cluster
3. Deploy manifests using Helm charts
4. Perform canary rollout with health checks
5. Monitor metrics and logs

### Step 8: Promote to Production
1. Merge staging branch to main via pull request
2. Require manual approval for production merge
3. ArgoCD syncs changes to production cluster
4. Execute canary rollout with rollback capability
5. Validate with health checks before full rollout

## Rollout Strategy

### Canary Deployment
- Initial traffic directed to new version (5-10%)
- Monitor metrics and error rates
- Gradually increase traffic if healthy
- Rollback automatically if issues detected

### Manual Approval Gates
- Production deployments require manual approval
- Health checks validate deployment success
- Rollback capability available during rollout

## Observability & Monitoring

### Metrics Collection
- Prometheus for metrics scraping
- metrics-server for Kubernetes metrics
- Custom metrics from applications

### Logging
- Centralized log collection
- EFS volumes for log storage
- Log aggregation and analysis

### Alerting
- Alert thresholds for error rates
- Scaling alerts from cluster-autoscaler
- Application-level alerting

## Results and Improvements

### Achieved Results
- **Scaling**: Vertical and horizontal scaling working
- **Security**: Zero trust model with mTLS, sealed secrets, and authorization policies
- **CI/CD**: Pipeline developed and operational up to Docker Hub publishing
- **GitOps**: GitOps repository working with app-of-apps pattern
- **Rollout**: Rollout and rollback strategies defined with Argo Rollouts

### Future Improvements
- Ansible for configuration management
- Enhanced observability and alerting
- Automated ArgoCD project management
- Improved control plane reliability
- Service mesh observability dashboards
- Automated scaling policies refinement

## Troubleshooting

### Common Issues
- **Pod not starting**: Check resource limits and cluster autoscaler
- **Certificate errors**: Verify cert-manager is running and issuer configured
- **ArgoCD sync failures**: Check GitOps repository connectivity and credentials
- **mTLS errors**: Verify PeerAuthentication policy in namespace
- **Storage issues**: Ensure EFS is mounted and accessible

See also: [Architecture](ARCHITECTURE.md) for detailed design, [CI/CD Pipeline](CICD_PIPELINE.md) for build process, [Kubernetes](KUBERNETES.md) for cluster configuration