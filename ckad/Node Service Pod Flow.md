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
|  Internal Node IP: 192.168.X.X                               |
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