# Node -> Service -> Pod flow




```sql
                            +--------------------------------------+
                            |          Kubernetes Cluster          |
                            |--------------------------------------|
                            |  Contains Nodes, Services, Pods      |
                            +--------------------------------------+
                                         |
                                         |  Request enters cluster 
                                         v
Client (browser)
     |
     |  HTTP request → NodeIP:NodePort
     v
+--------------------------------------------------------------+
|                  Kubernetes Nodes                            |
|--------------------------------------------------------------|
| Internal IP: 192.168.X.X (reachable in EKS, GKE, Hetzner, VM)|
|                                                              |
|  NodePort exposed here:                                      |
|      NodePort: 30080                                         |
|                                                              |
|  kube-proxy forwards NodePort → Service port                 |
+------------------------------+-------------------------------+
                               |
                               v
                   +-------------------------------------------+
                   |      Service: example-service             |
                   |-------------------------------------------|
                   |    Type: NodePort                         |
                   |                                           |
                   |    ClusterIP:     10.43.X.X               |
                   |    Service Port:  8080                    |
                   |                                           |
                   |    Selects Pods with: app=example         |
                   +------------------------+------------------+
                                            |
                                            | round-robin / load-balanced
                                            v
                                +-------------------------------+
                                | Pod: example-pod              |
                                |-------------------------------|
                                | containerPort / targetPort:   |
                                |                 5000          |
                                +-------------------------------+

```

```sql
Client
   → Kubernetes Node (192.168.X.X:30080)
       → Service (10.43.X.X:8080)
           → Pod (targetPort 5000)
```
### Pod YAML (container listens on port 5000)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  labels:
    app: example
spec:
  containers:
  - name: example-container
    image: example-image
    ports:
    - containerPort: 5000 #  app runs on port:5000
```

### Service YAML (NodePort → ServicePort → targetPort)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  type: NodePort
  selector:
    app: example
  ports:
  - port: 8080        # service port (ClusterIP port)
    targetPort: 5000  # pod containerPort
    nodePort: 30080   # externally exposed NodePort
    protocol: TCP
```

### FULL NETWORK DIAGRAM
```sql
                                    +--------------------------------------+
                                    |           Outside World              |
                                    | (your laptop / browser / API client) |
                                    +-------------------+------------------+
                                                        |
                                           HTTP request |
                                      http://NODE_IP:30080
                                                        v
                        --------------------------------------------------------
                        |                      Kubernetes Cluster              |
                        |------------------------------------------------------|
                        |  Contains:                                           |
                        |   - Nodes (VMs)                                      |
                        |   - Services                                         |
                        |   - Pods                                             |
                        |   - CNI network                                      |
                        -------------------------------------------------------
                                   |                         |
                                   |   Node receives traffic |  
                                   v                         |
+----------------------------------------------------------------------------------+
|                         Kubernetes Node (example-node)                           |
|----------------------------------------------------------------------------------|
|   External Node IP (may be public):   34.10.55.80    (optional)                  |
|   Internal Node IP (private network): 192.168.X.X    (reachable in EKS, GKE, VM) |
|                                                                                  |
|   NodePort Service exposes port:                                                 |
|            NodePort: 30080                                                       |
|                                                                                  |
|   Any traffic sent to:                                                           |
|            192.168.X.X:30080                                                     |
|        or  34.10.55.80:30080                                                     |
|   is forwarded into the cluster network via kube-proxy                           |
+-----------------------------------+----------------------------------------------+
                                        |
                                        | forwards NodePort → Service Port
                                        v
                     +------------------------------------------------------+
                     |              Service: example-service                |
                     |------------------------------------------------------|
                     |   ClusterIP (virtual service IP): 10.43.X.X          |
                     |   Service Port:                   8080               |
                     |   Type: NodePort                                     |
                     |   Selects: app=example                               |
                     +--------------------------+---------------------------+
                                                |
                                                | Load-balances to Pods
                                                v
                           +-------------------------------------------------+
                           |                Pod: example-pod                 |
                           |-------------------------------------------------|
                           |   Pod IP (CNI network):       172.17.X.X        |
                           |   containerPort / targetPort: 5000              |
                           |   The app is actually listening on port 5000    |
                           +-------------------------------------------------+

```


```text
                      🟦 EXTERNAL / PUBLIC NETWORK
┌──────────────────────────────────────────────┐
│ External Client (Browser / API Client)       │
└───────────────────┬──────────────────────────┘
                    │ 1. HTTPS request (https://example.com)
                    ▼
🟩 NODE LAYER (PUBLIC ↔ CLUSTER ENTRY)
┌──────────────────────────────────────────────┐
│ Node Public IP (VM, bare Metal)              │
│ 178.156.207.109                              │
└───────────────────┬──────────────────────────┘
                    │ 2. Traffic reaches node
                    ▼
🟨 INGRESS LAYER (L7 ROUTING)
┌───────────────────────────────────────────────────────────┐
│ nginx Ingress Controller Pod                              │
│ Pod IP: 10.42.0.5                                         │
│ DNS: 10-42-0-5.default.pod.cluster.local                  │
└──────────────────┬────────────────────────────────────────┘
                   │ 3. Ingress rule:
                   │     example.com → webapp-service
                   ▼
🟪 SERVICE LAYER (CLUSTER NETWORK, L4 ROUTING)
┌──────────────────────────────────────────────────────────────────────┐
│ Service: webapp-service (ClusterIP)                                  │
│ ClusterIP: 10.43.x.x                                                 │
│ Service Port: 8080                                                   │
│ NodePort (optional): 30080                                           │
│ DNS: webapp-service.default.svc.cluster.local                        │
│ Selector: app-label=my-backend                                       │
└──────────────────────┬───────────────────────────────────────────────┘
                       │ 4. kube-proxy selects a backend Pod
                       ▼

🟧 POD LAYER — BACKEND POD #1
┌────────────────────────────────────────────────────────────────┐
│ Backend Pod #1 — my-backend-1                                  │
│ Pod IP: 10.42.x.x                                              │
│ DNS: 10-42-1-21.default.pod.cluster.local                      │
│ TargetPort: 8080                                               │
│ ContainerPort: 8080                                            │
│                                                                │
│    🟥 CONTAINER LAYER (shared Pod network namespace)           │
│   ┌──────────────────────────────────────────────────────────┐ │
│   │ Container A (main app)                                   │ │
│   │ Exposes containerPort: 8080                              │ │
│   ├──────────────────────────────────────────────────────────┤ │
│   │ Container B (sidecar / log agent)                        │ │
│   │                                                          │ │
│   │ Containers inside Pod communicate via:                   │ │
│   │      localhost:<port>                                    │ │
│   └──────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────┘


🟧 POD LAYER — BACKEND POD #2
┌───────────────────────────────────────────────────────────────┐
│ Backend Pod #2 — webapp-2                                     │
│ Pod IP: 10.42.1.22                                             │
│ DNS: 10-42-1-22.default.pod.cluster.local                      │
│ TargetPort: 8080                                               │
│ ContainerPort: 8080                                            │
│ (Container details omitted for clarity)                        │
└───────────────────────────────────────────────────────────────┘


OPTIONAL PARALLEL PATH (NO INGRESS)
────────────────────────────────────
🟦 Client → http://178.156.207.109:30080  
    ▼  
🟩 NodePort: 30080  
    ▼ forwards to ▼  
🟪 Service Port 8080  
    ▼  
🟧 Pod TargetPort 8080  
    ▼  
🟥 ContainerPort 8080

```