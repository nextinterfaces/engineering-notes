# ⚡ Performance Considerations in AI Systems  
**Caching, Streaming, Latency Control**  


## 1. Why Performance Matters?  

Imagine your AI app is like a **barista making coffee ☕**:  
- If customers wait 5 minutes → they leave.  
- If service is instant → they come back daily.  

👉 In AI-powered apps: **speed = user experience = revenue**.  

---  

## 2. Caching 🗄️  

Cache = **save results so you don’t recompute**.  

### Types of Caching  
- **Exact-match cache**: If same input appears → reuse output.  
- **Semantic cache**: Store embeddings → reuse for semantically similar queries.  
- **Feature cache**: Store expensive preprocessing (e.g., embeddings, tokenization).  

```python
# Example: Redis caching for LLM results
import redis, hashlib
r = redis.Redis()

def cached_inference(prompt, model):
    key = hashlib.sha256(prompt.encode()).hexdigest()
    if r.exists(key):
        return r.get(key).decode()
    else:
        result = model.generate(prompt)
        r.set(key, result, ex=3600)  # expire in 1 hour
        return result
```  

✅ Reduces API cost, latency.  
❌ Risk of stale responses.  

---  

## 3. Streaming 📡  

Instead of waiting for full response → stream partial output.  

### Benefits  
- Improves **perceived latency** (users see progress).  
- Crucial for chatbots, real-time apps.  

```python
# FastAPI streaming response example
from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()

def generate():
    text = "Hello world from AI"
    for word in text.split():
        yield word + " "
        
@app.get("/stream")
def stream():
    return StreamingResponse(generate(), media_type="text/plain")
```  

✅ Keeps users engaged.  
❌ More complex infra (websockets, chunked responses).  

---  

## 4. Latency Control 🎛️  

Latency budget = **upper bound of response time**.  

### Techniques  
1. **Batching** → process multiple requests in one GPU call.  
2. **Distillation & quantization** → smaller models = faster inference.  
3. **Timeouts** → fail fast if API/tool too slow.  
4. **Load balancing** → distribute requests across replicas.  
5. **Asynchronous APIs** → free up resources while waiting.  

```python
# Example: timeout wrapper
import signal

class TimeoutException(Exception): pass

def handler(signum, frame): raise TimeoutException()

signal.signal(signal.SIGALRM, handler)
signal.alarm(2)  # max 2s
try:
    result = model.predict(x)
except TimeoutException:
    result = "Fallback response"
```  

✅ Guarantees SLA.  
❌ Trade-off: may return incomplete/approx answers.  

---  

## 5. System Design Text Diagram 🏗️  

```
[User Request]  
     ↓  
 [Cache?] → Hit → Return Fast  
     ↓ Miss  
 [Preprocessing Cache]  
     ↓  
 [Model Inference]  
     ↓  
 [Stream results to user]  
     ↓  
 [Monitor Latency + Apply Controls]
```  

---  

## 6. Challenges ⚠️  

- Cache invalidation (when to refresh results?).  
- Balancing streaming vs batch throughput.  
- GPU utilization vs latency trade-offs.  
- User perception vs backend cost.  

---  

## 7. Game Time 🎲  

Q1: Your chatbot repeats same answers often. Which optimization first?  
👉 Add **caching**.  

Q2: Users complain about waiting for 10s before reply starts. Fix?  
👉 Add **streaming**.  

Q3: Your SLA requires 500ms max latency, but model takes 2s. Fix?  
👉 **Quantize model, use batching, set timeouts**.  

---  

## 8. Recap 🎉  

- **Caching** = reuse results, cut cost & latency.  
- **Streaming** = improve perceived latency & UX.  
- **Latency control** = batching, distillation, load balancing, timeouts.  
