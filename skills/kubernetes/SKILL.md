---
name: kubernetes
description: "Deploy and manage containerized applications on Kubernetes clusters using manifests, GitHub Actions CI/CD, and ingress TLS configuration."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Kubernetes Skill

## Capabilities
- Deploy applications to Kubernetes clusters (k3s, k8s, microk8s, EKS, GKE, AKS)
- Write Kubernetes manifests: Deployment, Service, Ingress, Namespace, ConfigMap, Secret
- Configure TLS with cert-manager and Let's Encrypt
- Deploy via GitHub Actions using `kubectl`
- Manage namespaces and RBAC

## Workflow

### Step 1 — Assess the Deployment Target
Before writing any manifests:
- Identify the cluster type (k3s, k8s, managed cloud) and Kubernetes version
- Check what's already installed: nginx-ingress, cert-manager, existing namespaces
- Identify the application type: static SPA, API server, database-backed service
- Determine the ingress hostname and TLS requirements (Let's Encrypt vs existing cert)

### Step 2 — Write Kubernetes Manifests
Create all manifests in a `k8s/` directory at the project root.

**`k8s/namespace.yaml`** — isolate the app in its own namespace:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-app
```

**`k8s/deployment.yaml`** — replicas, image, resource limits, and health probes:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      imagePullSecrets:
        - name: ghcr-pull-secret
      containers:
        - name: my-app
          image: ghcr.io/org/my-app:sha-<sha>
          ports:
            - containerPort: 3000
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "500m"
              memory: "512Mi"
          livenessProbe:
            httpGet:
              path: /health
              port: 3000
            initialDelaySeconds: 10
            periodSeconds: 15
          readinessProbe:
            httpGet:
              path: /ready
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 10
```

**`k8s/service.yaml`** — ClusterIP pointing to the deployment:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
  namespace: my-app
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

**`k8s/ingress.yaml`** — nginx-ingress with cert-manager TLS:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app
  namespace: my-app
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - my-app.example.com
      secretName: my-app-tls
  rules:
    - host: my-app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-app
                port:
                  number: 80
```

### Step 3 — Configure GitHub Actions CI/CD
Create `.github/workflows/deploy.yml` for automated deploy on push to `main`:

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches: [main]

env:
  IMAGE_REPOSITORY: ghcr.io/org/my-app

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_REPOSITORY }}:sha-${{ github.sha }}

  deploy:
    runs-on: ubuntu-latest
    needs: build
    permissions:
      contents: read
      packages: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure kubeconfig
        run: |
          mkdir -p $HOME/.kube
          echo "${{ secrets.KUBECONFIG_B64 }}" | base64 -d > $HOME/.kube/config
          # Patch localhost to actual VPS IP so kubectl can reach the cluster remotely
          sed -i 's|https://127.0.0.1:|https://${{ secrets.VPS_IP }}:|g' $HOME/.kube/config
          sed -i 's|https://localhost:|https://${{ secrets.VPS_IP }}:|g' $HOME/.kube/config
          chmod 600 $HOME/.kube/config

      - name: Apply namespace
        run: kubectl apply -f k8s/namespace.yaml

      - name: Create GHCR pull secret
        env:
          GHCR_USERNAME: ${{ github.actor }}
        run: |
          GHCR_PASSWORD="${{ secrets.GHCR_TOKEN }}"
          if [ -z "$GHCR_PASSWORD" ]; then
            GHCR_PASSWORD="${{ secrets.GITHUB_TOKEN }}"
          fi
          kubectl create secret docker-registry ghcr-pull-secret \
            --namespace my-app \
            --docker-server=ghcr.io \
            --docker-username="$GHCR_USERNAME" \
            --docker-password="$GHCR_PASSWORD" \
            --dry-run=client -o yaml | kubectl apply -f -

      - name: Apply manifests
        run: kubectl apply -f k8s/

      - name: Update image
        run: |
          kubectl set image deployment/my-app \
            my-app=${IMAGE_REPOSITORY}:sha-${{ github.sha }} \
            -n my-app

      - name: Confirm rollout
        run: kubectl rollout status deployment/my-app -n my-app --timeout=120s

      - name: Debug rollout failure
        if: failure()
        run: |
          kubectl get pods -n my-app -o wide
          kubectl describe deployment my-app -n my-app
          POD_NAME=$(kubectl get pods -n my-app -l app=my-app -o jsonpath='{.items[0].metadata.name}' 2>/dev/null || true)
          if [ -n "$POD_NAME" ]; then
            kubectl describe pod "$POD_NAME" -n my-app
            kubectl logs "$POD_NAME" -n my-app --all-containers=true || true
            kubectl logs "$POD_NAME" -n my-app --all-containers=true --previous || true
          fi
          kubectl get events -n my-app --sort-by=.lastTimestamp | tail -n 20
```

Required GitHub Secrets:
| Secret | Description |
|---|---|
| `KUBECONFIG_B64` | Base64-encoded kubeconfig (with server patched to VPS IP — see Known Pitfalls) |
| `VPS_IP` | Public IP of the cluster API server (used to patch localhost in kubeconfig) |
| `GHCR_TOKEN` | Optional token for private GHCR pulls. Use when `GITHUB_TOKEN` cannot pull the package; otherwise the workflow can fall back to `GITHUB_TOKEN`. |

### Step 4 — Verify Deployment
After deploying, confirm everything is healthy:

```bash
# Check pods, image, and restarts
kubectl get pods -n my-app -o wide
kubectl describe deployment my-app -n my-app

# Check ingress was created and has an address
kubectl get ingress -n my-app
kubectl describe ingress my-app -n my-app

# Check TLS certificate status (Ready = issued by Let's Encrypt)
kubectl get certificate -n my-app
kubectl describe certificate my-app-tls -n my-app

# Check recent events for scheduling, pull, probe, or cert issues
kubectl get events -n my-app --sort-by=.lastTimestamp | tail -n 20

# Verify HTTP redirect and HTTPS response
curl -I http://my-app.example.com
curl -I https://my-app.example.com
```

## Anti-Patterns to Avoid
- ❌ Using `latest` image tag in production — use commit SHA (e.g. `sha-abc1234`) for traceability and rollback
- ❌ No resource `limits` — unguarded containers can exhaust node memory/CPU and evict other pods
- ❌ No `livenessProbe` / `readinessProbe` — Kubernetes will route traffic to unhealthy pods
- ❌ Deploying into the `default` namespace — makes multi-app clusters unmanageable; always use a named namespace
- ❌ Relying on `kubectl apply -f k8s/` without ensuring the namespace exists first
- ❌ Using different image repository or tag formats across build, deploy, and manifests
- ❌ Deploying private GHCR images without a registry secret, `imagePullSecrets`, and deploy job `packages: read`
- ❌ Waiting only for rollout timeout without printing pod, deployment, log, and event diagnostics on failure
- ❌ Using the deprecated `kubernetes.io/ingress.class` annotation instead of `spec.ingressClassName`
- ❌ Storing kubeconfig with `127.0.0.1` as the API server address in GitHub Secrets (see Known Pitfalls)
- ❌ Referencing `letsencrypt-prod` ClusterIssuer without verifying it exists — cert will silently fail to issue

## Known Pitfalls

### Pitfall: Namespace-dependent resources applied before namespace exists

`kubectl apply -f k8s/` does not guarantee the namespace manifest will be processed before other manifests in the directory. If `Deployment`, `Service`, `Ingress`, or `Secret` manifests reference `namespace: my-app` before the namespace exists, the deploy fails even though the namespace file is present.

Use an explicit two-step apply:

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/
```

Treat the namespace as bootstrap infrastructure, not as a manifest that can safely rely on directory ordering.

### Pitfall: Image repository/tag mismatch between build and deploy

Kubernetes deployments break in non-obvious ways when the build job pushes one image, the deploy job rolls out another, and the manifest points at a third repository or tag format. Common causes include:

- upper-case `OWNER/REPO` values leaking into a GHCR image reference
- build using `sha-${{ github.sha }}` while deploy uses another tag format
- manifests pointing at a different GHCR path than the workflow

Define one canonical lowercase repository such as `ghcr.io/org/my-app` and reuse it everywhere:

```yaml
env:
  IMAGE_REPOSITORY: ghcr.io/org/my-app
```

Then make build, deploy, and manifests all reference `${IMAGE_REPOSITORY}:sha-${{ github.sha }}`.

### Pitfall: Private GHCR pulls fail without registry secret / imagePullSecrets / packages:read

If the cluster pulls a private GHCR image, pushing successfully from CI is not enough. The cluster also needs credentials at runtime.

Make all three layers explicit:

1. Create or update a `docker-registry` secret in the target namespace
2. Reference that secret in the pod spec with `imagePullSecrets`
3. Give the deploy job `permissions: packages: read`

If available, prefer a dedicated `GHCR_TOKEN` secret for the registry secret creation step. If not, the workflow can fall back to `GITHUB_TOKEN`:

```yaml
permissions:
  contents: read
  packages: read
```

Without this setup, pods typically fail with `ImagePullBackOff` or `ErrImagePull`.

### Pitfall: Kubeconfig uses 127.0.0.1 as API server address

When a kubeconfig is generated on a self-hosted VPS (k3s, microk8s), the API server address defaults to `127.0.0.1:6443`. If this is base64-encoded and stored as `KUBECONFIG_B64` in GitHub Secrets without modification, GitHub Actions will fail with:

```
dial tcp 127.0.0.1:6443: connect: connection refused
```

**Fix A — patch after decoding in the workflow** (use when you don't control the secret value):
```yaml
- name: Configure kubeconfig
  run: |
    mkdir -p $HOME/.kube
    echo "${{ secrets.KUBECONFIG_B64 }}" | base64 -d > $HOME/.kube/config
    # Patch localhost to actual VPS IP so kubectl can reach the cluster remotely
    sed -i 's|https://127.0.0.1:|https://<VPS_IP>:|g' $HOME/.kube/config
    sed -i 's|https://localhost:|https://<VPS_IP>:|g' $HOME/.kube/config
    chmod 600 $HOME/.kube/config
```

**Fix B — fix at source before encoding** (preferred; do this once when setting up the secret):
```bash
# On the VPS, update the cluster server address
kubectl config set-cluster <cluster-name> --server=https://<VPS_IP>:6443

# Re-encode and update the GitHub Secret
cat ~/.kube/config | base64 | tr -d '\n'
# Paste the output into the KUBECONFIG_B64 GitHub Secret
```

Always verify the decoded kubeconfig points at a routable API server before debugging anything else:

```bash
kubectl config view --minify
kubectl cluster-info
```

### Pitfall: cert-manager ClusterIssuer doesn't exist

An `Ingress` with `cert-manager.io/cluster-issuer: letsencrypt-prod` silently fails to issue a TLS certificate if the `ClusterIssuer` resource doesn't exist in the cluster. Always verify before deploying:

```bash
kubectl get clusterissuer
```

If the issuer is missing, install cert-manager first:
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.yaml
```

Then create the ClusterIssuer:
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
      - http01:
          ingress:
            class: nginx
```

## When to Use
- Deploying containerized applications to a Kubernetes cluster
- Setting up automated Kubernetes deployments via GitHub Actions
- Configuring ingress with TLS for a custom domain
- Managing multi-service applications on k8s with namespace isolation
