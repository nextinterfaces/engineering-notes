# Ingress Egress Network Policies


```
          ┌──────────────────────────────┐
          │         INGRESS (IN)         │
          │  Allowed sources →           │
          └───────────────┬──────────────┘
                          ↓
                  +---------------+
                  |   POD(s)      |
                  | podSelector   |
                  +-------┬-------+
                          ↑
          ┌───────────────┴──────────────┐
          │         EGRESS (OUT)          │
          │  → Allowed destinations       │
          └──────────────────────────────┘
```
---

### Ingress Rules
```
                 +-------------------------------+
                 |         INGRESS RULES         |
                 |  (Allowed incoming sources)   |
                 |                               |
                 |  - from: PodSelector          |
                 |  - from: NamespaceSelector    |
                 |  - from: IPBlock              |
                 |                               |
                 +---------------+---------------+
                                 |
                                 |  Incoming traffic
                                 v
                        +---------------------+
                        |   TARGET PODS       |
                        | (spec.podSelector)  |
                        +---------------------+

```

### Exgress Rules
```sql
                        +---------------------+
                        |   TARGET PODS       |
                        | (spec.podSelector)  |
                        +---------------------+
                                 |
                                 |  Outgoing traffic
                                 v
                 +---------------+---------------+
                 |         EGRESS RULES          |
                 | (Allowed outgoing targets)    |
                 |                               |
                 |  - to: PodSelector            |
                 |  - to: NamespaceSelector      |
                 |  - to: IPBlock                |
                 |                               |
                 +-------------------------------+
```
### INGRESS NetworkPolicy Example
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: example-ingress-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: myapp              # TARGET PODS (receiving traffic)

  policyTypes:
    - Ingress

  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: backend-app    # Allowed Pod
        - namespaceSelector:
            matchLabels:
              env: prod-backends    # Allowed Namespace
        - ipBlock:
            cidr: 10.0.0.0/16 # Allowed IP range
      ports:
        - protocol: TCP
          port: 8080          # Target listening port
```

### EGRESS NetworkPolicy Example
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: example-egress-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: myapp              # TARGET PODS (sending traffic)

  policyTypes:
    - Egress

  egress:
    - to:
        - podSelector:
            matchLabels:
              app: database-pod-1   # Allowed Pod
        - namespaceSelector: 
            matchLabels:
              env: prod-databases    # Allowed Namespace
        - ipBlock:
            cidr: 192.168.0.0/24 # Allowed IP range
      ports:
        - protocol: TCP
          port: 3306          # Destination port
```

### Deny all INGRESS
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
  namespace: default
spec:
  podSelector: {}     # selects all pods in namespace
  policyTypes:
    - Ingress
```

### Deny all EGRESS
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-egress
  namespace: default
spec:
  podSelector: {}     # selects all pods in namespace
  policyTypes:
    - Egress
```