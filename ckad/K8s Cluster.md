# Kubernetes architecture

```
KUBERNETES CLUSTER
│
├── Node (Worker Machine)
│   │
│   ├── Kubelet (agent managing Pods on this Node)
│   ├── Container Runtime (e.g., containerd, Docker)
│   │
│   └── Pods (smallest deployable unit)
│        │
│        ├── Network (shared across all containers in the Pod)
│        ├── Storage Volumes (optional shared data)
│        │
│        └── Containers (one or more per Pod)
│             │
│             ├── App Process (e.g., your web server, API, worker)
│             ├── Dependencies (libraries, frameworks)
│             └── OS-level isolation (namespaces, cgroups)
│
└── Control Plane
    ├── API Server (entry point for all commands)
    ├── Scheduler (decides which Node runs each Pod)
    ├── Controller Manager (ensures desired state)
    └── etcd (stores cluster state)

```

```
KUBERNETES CLUSTER
│
├── Control Plane (manages desired state)
│   ├── kube-apiserver         → Front door for all commands (kubectl, etc.)
│   ├── controller-manager      → Ensures actual = desired state
│   ├── scheduler               → Decides which Node runs each Pod
│   └── etcd                    → Stores all cluster data (configuration, state)
│
├── Namespaces (logical partitions inside the cluster)
│   │
│   ├── Workloads
│   │   │
│   │   ├── Deployment
│   │   │    ├── ReplicaSet
│   │   │    │    ├── Pod (smallest runnable unit)
│   │   │    │    │    ├── Containers (1 or more)
│   │   │    │    │    │    ├── App process (e.g., web server)
│   │   │    │    │    │    └── Sidecar containers (e.g., logger, proxy)
│   │   │    │    │    └── Shared network + storage volumes
│   │   │    │    └── (Multiple Pods for scaling)
│   │   │    └── Controls rollout and updates
│   │   │
│   │   ├── StatefulSet  → Like Deployment, but stable identity and storage
│   │   ├── DaemonSet    → Runs one Pod per Node (e.g., log collector)
│   │   ├── Job / CronJob → Run Pods for finite or scheduled tasks
│   │   └── ReplicaSet (standalone) → Maintains fixed number of Pods
│   │
│   ├── Networking
│   │   ├── Service
│   │   │    ├── ClusterIP (internal load balancer)
│   │   │    ├── NodePort  (expose on each Node’s port)
│   │   │    └── LoadBalancer (external access)
│   │   ├── Ingress        → HTTP routing to Services (layer 7)
│   │   ├── NetworkPolicy  → Controls which Pods can talk to each other
│   │   └── Endpoints      → IPs of actual Pod backends for each Service
│   │
│   ├── Configuration
│   │   ├── ConfigMap → Plain-text config injected into Pods
│   │   └── Secret    → Encrypted data (passwords, tokens)
│   │
│   ├── Storage
│   │   ├── PersistentVolume (PV)   → Actual storage resource
│   │   ├── PersistentVolumeClaim (PVC) → Pod’s request for storage
│   │   └── StorageClass → Defines how storage is provisioned
│   │
│   ├── Security
│   │   ├── ServiceAccount → Identity for Pods to talk to API
│   │   ├── Role / ClusterRole → Permissions definition
│   │   ├── RoleBinding / ClusterRoleBinding → Grants access
│   │   └── PodSecurityPolicy (deprecated → replaced by Pod Security Standards)
│   │
│   └── Observability
│       ├── Event → Records cluster actions or errors
│       ├── Pod Logs → Application output
│       ├── Metrics → From kubelet, apps, or Prometheus
│       └── Probe (liveness/readiness/startup) → Health checks for Pods
│
└── Nodes (worker machines)
    ├── kubelet → Manages Pods on this Node
    ├── kube-proxy → Handles networking to Services
    └── Container Runtime → Runs containers (containerd, CRI-O, etc.)
```

### Topology
```sql
                  +-------------------------------+
                  |        AWS EKS Cluster        |
                  +-------------------------------+
                      |                 |
                      |                 |
         +------------+----+      +-----+----------+
         | Control Plane   |      | Worker Nodes   |
         | (managed by AWS)|      | (EC2 Instances)|
         +-----------------+      +----------------+
                                          |
                   ---------------------------------------------------
                   |                   |                  |          |
              +---------+       +-----------+       +-----------+   +----------+
              | Node 1  |       | Node 2    |       | Node 3    |   | Node N   |
              +---------+       +-----------+       +-----------+   +----------+
                   |                 |                  |
         ------------------------------------------------------------------
         |                   |                    |                      |
+----------------+  +------------------+  +-------------------+  +------------------+
| Namespace: dev |  | Namespace: qa    |  | Namespace: stage  |  | Namespace: prod |
+----------------+  +------------------+  +-------------------+  +------------------+
       |                     |                     |                     |
+-------------+    +----------------+     +-----------------+     +----------------+
| Pods (dev)  |    | Pods (qa)      |     | Pods (stage)    |     | Pods (prod)    |
| run on      |    | run on         |     | run on          |     | run on         |
| Node 1/2/3  |    | Node 2/3       |     | Node 1/3        |     | Node 1/2       |
+-------------+    +----------------+     +-----------------+     +----------------+

```