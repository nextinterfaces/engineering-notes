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
