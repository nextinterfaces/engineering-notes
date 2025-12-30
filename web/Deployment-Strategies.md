# Deployment Strategies


## 1. Recreate (All-at-Once)
**Description:**  
The old version is stopped completely before the new version is deployed.

**Pros:**
- Very simple
- No extra infrastructure

**Cons:**
- Downtime during deployment
- Risky for production systems

**Use when:**
- Internal tools
- Low-traffic or non-critical systems

---

## 2. Rolling Update
**Description:**  
Instances are updated gradually while traffic continues to flow.

**Pros:**
- No downtime
- Default in many orchestrators

**Cons:**
- Temporary version skew
- Requires backward-compatible APIs

**Use when:**
- Stateless services
- Kubernetes-based workloads

---

## 3. Blue-Green Deployment
**Description:**  
Two identical environments exist. Traffic is switched from old (blue) to new (green) instantly.

**Pros:**
- Instant rollback
- Clean separation between versions

**Cons:**
- Requires double infrastructure

**Use when:**
- Critical systems
- Need fast rollback guarantees

---

## 4. Canary Deployment
**Description:**  
A small percentage of users are routed to the new version before full rollout.

**Pros:**
- Limits blast radius
- Detects issues early

**Cons:**
- Requires strong monitoring
- More complex traffic management

**Use when:**
- User-facing applications
- High confidence required before full rollout
