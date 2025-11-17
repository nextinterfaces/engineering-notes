# OSI Model -- Text Diagram and Explanation

                  ┌───────────────────────────────────────┐
     Layer 7      │  APPLICATION                          │
     (L7)         │  - HTTP, HTTPS, DNS, SMTP, gRPC       │
                  │  - What users & apps interact with    │
                  └───────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 6       │  PRESENTATION                        │
     (L6)          │  - Encryption (TLS/SSL)              │
                   │  - Compression, serialization (JSON) │
                   └──────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 5       │  SESSION                             │
     (L5)          │  - Session setup/teardown            │
                   │  - Authentication, API sessions      │
                   └──────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 4       │  TRANSPORT                           │
     (L4)          │  - TCP, UDP                          │
                   │  - Ports (80, 443), reliability      │
                   └──────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 3       │  NETWORK                             │
     (L3)          │  - IP addresses, routing             │
                   │  - Routers                           │
                   └──────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 2       │  DATA LINK                           │
     (L2)          │  - MAC addresses                     │
                   │  - Switches                          │
                   └──────────────────────────────────────┘
                   ┌──────────────────────────────────────┐
     Layer 1       │  PHYSICAL                            │
     (L1)          │  - Fiber, copper, radio waves        │
                   │  - Bits on the wire                  │
                   └──────────────────────────────────────┘

### **L7 --- Application Layer**

Protocols like HTTP, HTTPS, DNS, SMTP. Where users and apps interact.

### **L6 --- Presentation Layer**

TLS/SSL encryption, JSON/Protobuf serialization, compression.

### **L5 --- Session Layer**

Maintains API sessions, connections, WebSockets.

### **L4 --- Transport Layer**

TCP, UDP, ports, reliability, ordering.

### **L3 --- Network Layer**

IP addressing, routing, routers.

### **L2 --- Data Link Layer**

MAC addresses, switches.

### **L1 --- Physical Layer**

Raw bits over fiber, copper, WiFi.

---

## Quick mapping to Kubernetes

- **L7 — Application**
  - HTTP/gRPC apps in Pods, CoreDNS for cluster DNS, Ingress resources and controllers.
  - Service mesh (e.g., Istio/Linkerd) adds L7 routing, retries, and observability.
- **L6 — Presentation**
  - TLS termination at Ingress Controllers; mTLS between services via a mesh.
  - Serialization formats: JSON/Proto used by app payloads.
- **L5 — Session**
  - App-level sessions/WebSockets. Sticky sessions via Ingress/Service annotations when needed.
- **L4 — Transport**
  - TCP/UDP. Services expose ClusterIP/NodePort/LoadBalancer. kube-proxy programs iptables/IPVS.
  - Readiness/liveness/startup probes are L4/L7 checks that drive endpoints.
- **L3 — Network**
  - Pod IPs and routing provided by CNI plugins (Calico, Cilium, Flannel).
  - NetworkPolicies operate here to allow/deny Pod-to-Pod traffic.
- **L2 — Data Link**
  - Node NICs, bridges, and veth pairs. Some CNIs use L2/L3 overlays or eBPF.
- **L1 — Physical**
  - Underlay network: switches, links, wireless; outside Kubernetes’ direct control.

## Protocol Data Units (PDU)

- **L7:** Data (application messages)
- **L4:** Segments (TCP) / Datagrams (UDP)
- **L3:** Packets
- **L2:** Frames
- **L1:** Bits

## Common ports and K8s specifics

- **80/443:** HTTP/HTTPS to Services/Ingress.
- **53/UDP,TCP:** DNS (CoreDNS service `kube-dns`).
- **6443/TCP:** kube-apiserver.
- **2379-2380/TCP:** etcd.
- **10250/TCP:** kubelet.
- **30000–32767/TCP,UDP:** NodePort range (default).


## Mnemonics

- Top-down: "Application, Presentation, Session, Transport, Network, Data Link, Physical".
- Bottom-up: "Please Do Not Throw Sausage Pizza Away".
