# Kubernetes

Open source system for automating deployment, scaling, and management of containerized applications.

Notes from deploying SynergyChat on minikube (k8sfordummies project).

---

## Core concepts

- By default, resources inside kubernetes run in a private, isolated network. They can see other resources in the cluster, but not the outside world.
- **Pods** are the simplest unit you create or deploy — one or more running containers. A pod is basically an abstraction over a container (a running instance of an image). Each pod gets a unique internal k8s IP, which makes communication easier.
- Pods are **ephemeral**. Temporary by nature — don't store important things on them. If you manually delete a pod, the deployment controller just brings it back.
- **Deployments** are big ole yaml files you can edit. You describe the state you want; kubernetes's job is to get and keep it there. `replicas` is one config you can change to spin up more pods.
- **ReplicaSets** keep a stable set of replica pods running. If you say you want 10 pods, it keeps 10 running. Deployments are a higher-level abstraction that manage ReplicaSets for you — you probably won't touch ReplicaSets directly.
- Apply configs with `kubectl apply -f <file>`. You can also edit live resources with `kubectl edit`.
- **"Thrashing pods"** = a pod keeps crashing and restarting.

### Minikube vs production

- Minikube is a single-node cluster. Production k8s is usually multi-node.
- Kubernetes breaks things into **resources** (CPU, GPU, memory, disk) and **nodes** (your machines). Pods are your apps. On minikube there's only 1 node, so you can't really add more.

---

## ConfigMaps

- Decouple config from container images so you don't have to rebuild every time you change a value.
- **Not secure** — use Kubernetes Secrets or a third-party solution for sensitive stuff.

SynergyChat examples:
| File | Key vars |
|------|----------|
| `api-configmap.yaml` | `API_PORT`, `CRAWLER_BASE_URL`, `API_DB_FILEPATH` |
| `web-configmap.yaml` | `WEB_PORT`, `API_URL` |
| `crawler-configmap.yaml` | `CRAWLER_PORT`, `CRAWLER_KEYWORDS`, `CRAWLER_DB_PATH` |

Inject via `envFrom` (whole map) or individual `env` + `configMapKeyRef`.

---

## Services

Provide a stable endpoint for pods and load balance across a group. If underlying pods are created/destroyed, the endpoint stays the same.

- **ClusterIP** (default) — internal virtual IP. Only reachable inside the cluster.
- Other types build on each other: **NodePort**, **LoadBalancer**, **ExternalName**.

SynergyChat services:
| File | Type | Notes |
|------|------|-------|
| `web-service.yaml` | ClusterIP | port 80 → pod 8080 |
| `api-service.yaml` | NodePort | nodePort 30080 |
| `crawler-service.yaml` | ClusterIP | in `crawler` namespace |

---

## Gateway API

Gateways are the newer alternative to **Ingress**. A Gateway describes traffic-handling infrastructure (filtering, balancing, splitting). **HTTPRoute** resources set routing rules.

More common in cloud deployments, but we used it locally with minikube + Envoy Gateway.

**SynergyChat routing:**
| File | Purpose |
|------|---------|
| `app-gatewayclass.yaml` | Links gateway to Envoy controller |
| `app-gateway.yaml` | Listens on HTTP port 80 |
| `web-httproute.yaml` | `synchat.internal` → `web-service` |
| `api-httproute.yaml` | `synchatapi.internal` → `api-service` |

Gateways aren't core k8s API, but they're not really 3rd party either — lots of extensions exist for cloud providers (GKE, AWS, etc.) with provider-specific annotations like `networking.gke.io/certmap`.

### Local DNS setup

External traffic flow:
```
DNS → IP Address → Gateway → Service → Pod
```

For local dev (not global DNS):
1. Run `minikube tunnel -c` to expose the gateway load balancer on `127.0.0.1:80`
2. Add to `/etc/hosts` (and Windows hosts file on WSL):
   ```
   127.0.0.1   synchat.internal
   127.0.0.1   synchatapi.internal
   ```

Watch out for **IIS on Windows** hogging port 80 — stop the W3SVC service if you hit the IIS welcome page instead of SynergyChat.

After gateway was working, connected the web frontend to the API via `web-configmap.yaml` (`API_URL: http://synchatapi.internal`).

---

## Storage

Pods have a filesystem, but it's ephemeral — deleted when the pod is deleted.

- **Volumes** solve persistence and data sharing across containers. Many types; some are ephemeral (data sharing only).
- Scale either multiple containers in one pod, or multiple pods with one container each. When containers need to share resources (like our 3 crawlers sharing a DB), put them in the same pod.

### Persistent volumes

- **PV** — the actual storage. Static PVs are created manually; dynamic PVs are created automatically when requested (usually better/less work).
- **PVC** — a request for storage. Declarative "I need X amount of space."

SynergyChat: `api-pvc.yaml` → mounted at `/persist` in `api-deployment.yaml`. Messages saved to `/persist/db.json` via `API_DB_FILEPATH`.

Delete pods, no data loss!

**Note:** using a persistent volume as your DB isn't usually the best move long-term — managed external DBs are the norm. Fine for learning though.

### Crawler shared storage

Crawler uses `emptyDir` at `/cache` with `CRAWLER_DB_PATH: /cache/db` so all 3 containers in the pod share the same filesystem DB.

---

## Namespaces

Isolate cluster resources into logical groups — kind of like directories or tags. Same resource name can exist in different namespaces.

```bash
kubectl get ns
kubectl -n crawler get pods    # without -n, defaults to "default"
```

Namespaces also affect **internal DNS**:
```
<service-name>.<namespace>.svc.cluster.local
```

Shorthand that usually works:
- Same namespace: `http://crawler-service`
- Cross-namespace: `http://crawler-service.crawler`

Crawler lives in its own `crawler` namespace. API (in `default`) reaches it via:
```
CRAWLER_BASE_URL: http://crawler-service.crawler.svc.cluster.local
```

Internal comms > external when you can — faster, no public DNS needed, more secure (usually no HTTPS required inside the cluster).

---

## Resource limits & scaling

```bash
kubectl top pod          # like unix top — needs metrics-server (minikube addon)
kubectl describe pod <pod-name>
```

- **Vertical scaling** ("scaling up") — more CPU/RAM to a node or pod.
- **Horizontal scaling** — more pod instances (or more nodes). Usually better in k8s.

Set limits in deployment yaml:
```yaml
resources:
  limits:
    memory: 256Mi
    cpu: 50m
  requests:
    memory: 10Mi
    cpu: 25m
```

- Memory measured in `Ki`, `Mi`, `Gi`. CPU in cores or milli-cores (`500m` = 0.5 cores).
- CPU can be **throttled** — app just goes slower.
- Memory runs out → pod **crashes** (`OOMKilled` status on `kubectl get pods`).

### HPA (Horizontal Pod Autoscaler)

Automatically scales pod count based on CPU utilization (or custom metrics). Common pattern: low base replicas + autoscaling.

SynergyChat test apps:
| File | Deployment | Behavior |
|------|------------|----------|
| `testcpu-deployment.yaml` + `testcpu-hpa.yaml` | CPU hog | Scales up under load |
| `web-deployment.yaml` + `web-hpa.yaml` | Static web server | Stays at min replicas (idle) |

HPA needs CPU **requests** set to calculate utilization percentage. Without requests, you'll see `<unknown>/50%`.

Remove `replicas:` from a deployment to let HPA control pod count.

---

## Nodes & scheduling

Everything done on single-node minikube works the same on multi-node production (GKE, EKS, AKS, or roll-your-own on EC2/bare metal).

Two machine types:
- **Control plane** — manages the cluster (API server, scheduler, controller manager). Used to be called master nodes. Fairly static.
- **Worker nodes** — what people usually mean by "nodes." This is what you autoscale.

Managed k8s (GKE, EKS, AKS) handles a lot of control plane stuff for you.

### Requests vs limits

- **Requests** — what a pod asks from the node. Used for **scheduling**. Too high = k8s thinks nodes are full when they're not (wasted capacity). Too low = pods crash once scheduled.
- **Limits** — safety net. Where you don't want usage to go in normal operation. Hitting limits = investigate (bug? more traffic than expected? raise limits or optimize code).

Good rules of thumb:
- Memory requests: ~10% above average pod memory use
- CPU requests: ~50% of average CPU use
- Memory limits: ~100% above average memory use
- CPU limits: ~100% above average CPU use

Memory is the worst resource to run out of. CPU throttles; memory crashes. Leave a buffer.

This is an art, not a science.

Absurdly high requests (like 40GB on testram) → pod stuck **Pending** with `Insufficient memory`. Scheduler won't place it even though the app only uses ~10MB.

---

## SynergyChat manifest index

| File | Kind | Namespace |
|------|------|-----------|
| `web-deployment.yaml` | Deployment | default |
| `web-configmap.yaml` | ConfigMap | default |
| `web-service.yaml` | Service | default |
| `web-httproute.yaml` | HTTPRoute | default |
| `web-hpa.yaml` | HPA | default |
| `api-deployment.yaml` | Deployment | default |
| `api-configmap.yaml` | ConfigMap | default |
| `api-service.yaml` | Service | default |
| `api-httproute.yaml` | HTTPRoute | default |
| `api-pvc.yaml` | PVC | default |
| `crawler-deployment.yaml` | Deployment | crawler |
| `crawler-configmap.yaml` | ConfigMap | crawler |
| `crawler-service.yaml` | Service | crawler |
| `app-gatewayclass.yaml` | GatewayClass | default |
| `app-gateway.yaml` | Gateway | default |
| `testcpu-deployment.yaml` | Deployment | default |
| `testcpu-hpa.yaml` | HPA | default |
| `testram-deployment.yaml` | Deployment | default |
| `testram-configmap.yaml` | ConfigMap | default |

---

## Useful commands

```bash
kubectl apply -f <file>
kubectl get pods
kubectl get pods -A                    # all namespaces
kubectl get pods -n crawler
kubectl get deployments,services,configmaps,pvc,hpa
kubectl logs <pod-name>
kubectl logs <pod-name> --all-containers
kubectl delete pod <pod-name>
kubectl describe pod <pod-name>
kubectl top pod
kubectl get hpa -w
kubectl rollout restart deployment/<name>
minikube tunnel -c
```
