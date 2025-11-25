
---

# **Installing Istio and ArgoCD, and Exposing ArgoCD on EC2 kubeadm Cluster**

## **Introduction**

This guide will walk you through installing **Istio** and **ArgoCD** on a Kubernetes cluster created using **kubeadm on EC2**, and exposing ArgoCD **via the EC2 public IP** using the Istio Ingress Gateway.
Because EC2 + kubeadm does **not** support LoadBalancer services, we expose Istio using a **NodePort** and forward port 80 → 30080

---

# **Step 1: Install Istio**

Download Istio:

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.28.0
export PATH=$PWD/bin:$PATH
```

Install Istio (demo profile):

```bash
istioctl install --set profile=demo -y
```

Verify installation:

```bash
kubectl get pods -n istio-system
```

You should see pods like:

```
istio-ingressgateway
istiod
istio-egressgateway
```

---

# **Step 2: Change Istio IngressGateway to NodePort**

EC2 kubeadm clusters cannot assign external IPs for LoadBalancer services.
So, convert the Istio ingressgateway into a NodePort:

```bash
kubectl patch svc istio-ingressgateway -n istio-system -p '{"spec": {"type": "NodePort"}}'
```

Verify NodePort:

```bash
kubectl get svc -n istio-system istio-ingressgateway
```

Look for:

```
80:30080/TCP
```

Here, **30080** is your NodePort.

---

# **Step 3: Create an Istio Gateway (for any host/IP)**

Create file:

```bash
cat <<EOF | sudo tee /home/admin/argocd-gateway.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: argocd-gateway
  namespace: istio-system
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
EOF
```

Apply:

```bash
kubectl apply -f /home/admin/argocd-gateway.yaml
```

---

# **Step 4: Install ArgoCD**

Create namespace:

```bash
kubectl create namespace argocd
```

Install ArgoCD:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Check pods:

```bash
kubectl get pods -n argocd
```

---

# **Step 5: Create an Istio VirtualService to expose ArgoCD**

Create file:

```bash
cat <<EOF | sudo tee /home/admin/argocd-virtualservice.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: argocd-vs
  namespace: argocd
spec:
  hosts:
  - "*"
  gateways:
  - istio-system/argocd-gateway
  http:
  - route:
    - destination:
        host: argocd-server.argocd.svc.cluster.local
        port:
          number: 80
EOF
```

Apply:

```bash
kubectl apply -f /home/admin/argocd-virtualservice.yaml
```

---

# **Step 6: Forward EC2 Port 80 → Istio NodePort (30080)**

Run:

```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 30080
```

Persist:

```bash
sudo apt-get install -y iptables-persistent
sudo netfilter-persistent save
```

---

# **Step 7: Allow Traffic in EC2 Security Group**

In AWS EC2 Security Group:

| Type       | Port  | Source    | Purpose                           |
| ---------- | ----- | --------- | --------------------------------- |
| HTTP       | 80    | 0.0.0.0/0 | Public ArgoCD access              |
| Custom TCP | 30080 | 0.0.0.0/0 | (Optional) NodePort direct access |

Save changes.

---

# **Step 8: Access ArgoCD**

Open a browser:

```
http://<EC2_PUBLIC_IP>:30080
```

---

# **Step 9: Retrieve ArgoCD Admin Password**

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

Login in browser:

* **Username:** `admin`
* **Password:** (value retrieved above)

---

✔ Installed Istio
✔ Installed ArgoCD
✔ Configured Gateway + VirtualService
✔ Converted LoadBalancer → NodePort
✔ Exposed ArgoCD over plain HTTP


