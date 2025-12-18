# Kubernetes

## K8s Architecture (GitOps, Helm)

### Kubernetes Cluster Structure
The cluster is organized into namespaces with Istio service mesh injection:

- **kube-system**: Core Kubernetes components
  - cluster-autoscaler: Automatically scales nodes based on demand
  - metrics-server: Collects resource usage metrics
  - SealedSecrets-controller: Manages encrypted secrets
  - AWS EFS CSI Driver: Enables persistent storage via AWS EFS

- **istio-system**: Service mesh components
  - Istio Pilot: Manages service mesh configuration
  - Ingress Gateway: Routes external traffic
  - Virtual Services: Traffic routing policies
  - Certificates: TLS certificate management

- **g11-prod** (Production - Istio injected):
  - Frontend pods with rolling updates
  - Frontend rollout with canary strategy
  - Frontend Analysis template for metrics
  - User, content, comment, category, and media services
  - SealedSecrets for sensitive configuration
  - Storage Class (EFS) for persistent volumes
  - PeerAuthentication: Enforces mTLS between pods

- **g11-stg** (Staging - Istio injected):
  - Identical to production for consistency
  - Used for testing before production release

### Data Layer
- **PostgreSQL**: Managed via CrunchyPostgres Operator
- **PostgresCluster**: Primary database for all services

## GitOps Deployment Model

### HELM Templates
- Kubernetes resources defined as Helm charts
- Parameterized configuration for different environments
- Values files for staging and production

### ArgoCD Integration
- **Staging Branch (g11-stg)**: Tracked by ArgoCD in staging cluster
- **Production Branch (g11-prod)**: Tracked by ArgoCD in production cluster
- Continuous synchronization with cluster state

## Helm Charts

### Chart Organization
- Each microservice has its own Helm chart
- Database charts for PostgreSQL deployment
- Infrastructure charts for system components

### Deployment Process
1. Push changes to GitOps repository (staging branch)
2. ArgoCD detects changes and syncs with staging cluster
3. Canary rollout validation
4. On success, merge staging to main (production branch)
5. Manual approval required for production
6. Production ArgoCD syncs with production cluster
7. Health checks and rollback capability enabled

### App-of-Apps Pattern
- Single root Argo Application that manages multiple applications
- Centralized control of all microservices
- Simplified management of complex deployments

See also: [Architecture](ARCHITECTURE.md) for cluster details, [Deployment](DEPLOYMENT.md) for rollout strategies, [Infrastructure](INFRASTRUCTURE.md) for Terraform automation