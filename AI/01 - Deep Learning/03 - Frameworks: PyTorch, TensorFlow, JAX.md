# ⚙️ Deep Learning Frameworks: PyTorch, TensorFlow & JAX  
 

## 1. Why Frameworks?  

Imagine building a rocket 🚀.  
- You **could** solder every wire yourself (raw NumPy).  
- Or use **NASA-grade tools** (frameworks).  

👉 PyTorch, TensorFlow, and JAX = power tools for **training, scaling, and deploying neural nets**.  

---  

## 2. PyTorch – The Researcher’s Best Friend 🧪  

- **Dynamic Computation Graph**: “define by run”. Graph is built as code executes.  
- **Imperative style**: Debugging feels like standard Python.  
- **Huge community**: Dominates academia & startups.  

### ✅ Pros  
- Very Pythonic & intuitive.  
- Easy debugging with breakpoints.  
- Rich ecosystem (TorchVision, TorchText, PyTorch Lightning).  
- Strong support in research papers.  

### ❌ Cons  
- Historically weaker deployment (though TorchScript & ONNX improved this).  
- Less mobile/embedded support compared to TensorFlow.  
- Multi-language support weaker than TensorFlow.  


### 2.1 Minimal MLP (retained)
```python
import torch
import torch.nn as nn
import torch.optim as optim

class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(10, 5)
        self.fc2 = nn.Linear(5, 1)

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        return torch.sigmoid(self.fc2(x))

model = Net()
opt = optim.SGD(model.parameters(), lr=1e-2)
loss_fn = nn.BCELoss()

x = torch.randn(4, 10)
y = torch.ones(4, 1)
loss = loss_fn(model(x), y)
loss.backward()
opt.step()
```

### 2.2 Dataset/DataLoader + Training Loop
```python
from torch.utils.data import Dataset, DataLoader

class ToyDS(Dataset):
    def __init__(self, n=512, d=10):
        self.x = torch.randn(n, d)
        self.y = (self.x.sum(dim=1, keepdim=True) > 0).float()
    def __len__(self): return len(self.x)
    def __getitem__(self, i): return self.x[i], self.y[i]

ds = ToyDS()
loader = DataLoader(ds, batch_size=32, shuffle=True)

model = Net()
opt = optim.Adam(model.parameters(), lr=1e-3)
loss_fn = nn.BCELoss()

for epoch in range(5):
    for xb, yb in loader:
        opt.zero_grad()
        pred = model(xb)
        loss = loss_fn(pred, yb)
        loss.backward()
        opt.step()
```

### 2.3 Mixed Precision + CUDA (when available)
```python
scaler = torch.cuda.amp.GradScaler(enabled=torch.cuda.is_available())
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = Net().to(device)

for xb, yb in loader:
    xb, yb = xb.to(device), yb.to(device)
    opt.zero_grad(set_to_none=True)
    with torch.cuda.amp.autocast(enabled=torch.cuda.is_available()):
        pred = model(xb)
        loss = loss_fn(pred, yb)
    scaler.scale(loss).backward()
    scaler.step(opt)
    scaler.update()
```

### 2.4 TorchScript (Scripting & Tracing) + C++/Mobile readiness
```python
# Scripting: supports control flow
scripted = torch.jit.script(model)
scripted.save("model_scripted.pt")

# Tracing: for purely feed-forward graphs
example = torch.randn(1, 10, device=device)
traced = torch.jit.trace(model, example)
traced.save("model_traced.pt")
```

### 2.5 Export to ONNX (interop with other runtimes)
```python
dummy = torch.randn(1, 10)
torch.onnx.export(model.cpu(), dummy, "model.onnx", input_names=["input"], output_names=["output"], opset_version=17)
```


---  

## 3. TensorFlow – The Production Powerhouse 🏭  

- **Static Graph (TF1)**: Optimize whole computation graph before execution.  
- **Eager Execution (TF2)**: More PyTorch-like, easier debugging.  
- **Keras API**: High-level, beginner-friendly.  
- **Deployment Strength**: TF Lite (mobile), TF.js (browser), TF Serving (server).  

### ✅ Pros  
- Industry adoption (Google, production ML teams).  
- Scales across GPUs/TPUs seamlessly.  
- Cross-language support (Java, Go, JavaScript, C++).  
- Strong deployment pipeline (edge → cloud).  

### ❌ Cons  
- TF1.x was notoriously complex.  
- Still more boilerplate than PyTorch.  
- Debugging graphs is harder than eager PyTorch.  


### 3.1 Minimal Keras MLP (retained)
```python
import tensorflow as tf
from tensorflow.keras import layers

model = tf.keras.Sequential([
    layers.Dense(5, activation="relu", input_shape=(10,)),
    layers.Dense(1, activation="sigmoid")
])
model.compile(optimizer="adam", loss="binary_crossentropy")
import numpy as np
x = np.random.randn(32, 10).astype("float32")
y = (x.sum(axis=1, keepdims=True) > 0).astype("float32")
model.fit(x, y, epochs=3, verbose=0)
```

### 3.2 tf.data Pipeline + Custom Training Loop (GradientTape)
```python
def make_ds(n=2048, d=10, batch=64):
    x = tf.random.normal((n, d))
    y = tf.cast(tf.reduce_sum(x, axis=1, keepdims=True) > 0, tf.float32)
    ds = tf.data.Dataset.from_tensor_slices((x, y)).shuffle(1024).batch(batch)
    return ds

ds = make_ds()

class M(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.d1 = layers.Dense(64, activation="relu")
        self.d2 = layers.Dense(1, activation="sigmoid")
    def call(self, x):
        return self.d2(self.d1(x))

model = M()
opt = tf.keras.optimizers.Adam(1e-3)
loss_fn = tf.keras.losses.BinaryCrossentropy()

@tf.function  # XLA-eligible graph + AutoGraph
def train_step(xb, yb):
    with tf.GradientTape() as tape:
        pred = model(xb, training=True)
        loss = loss_fn(yb, pred)
    grads = tape.gradient(loss, model.trainable_variables)
    opt.apply_gradients(zip(grads, model.trainable_variables))
    return loss

for epoch in range(3):
    for xb, yb in ds:
        _ = train_step(xb, yb)
```

### 3.3 XLA Compilation (per-op or whole function)
```python
tf.config.optimizer.set_jit(True)  # enable XLA globally where possible
# Or annotate a function:
@tf.function(jit_compile=True)
def fast_step(xb, yb):
    with tf.GradientTape() as tape:
        pred = model(xb, training=True)
        loss = loss_fn(yb, pred)
    grads = tape.gradient(loss, model.trainable_variables)
    opt.apply_gradients(zip(grads, model.trainable_variables))
    return loss
```

### 3.4 SavedModel + TF Serving signature
```python
# save
model.save("exported_model")

# infer signature example
@tf.function(input_signature=[tf.TensorSpec([None, 10], tf.float32)])
def serve_fn(x):
    return {"output": model(x)}

tf.saved_model.save(model, "exported_model_sig", signatures={"serving_default": serve_fn})
```

### 3.5 Edge/Browser variants (heads-up)
- **TF Lite**: convert SavedModel → `.tflite` for Android/iOS/microcontrollers.  
- **TF.js**: run a smaller model in the **browser/Node** (TypeScript/JS).  


---  

## 4. JAX – The New Kid with Superpowers ⚡  

- **NumPy + Autograd + XLA compiler**.  
- Functional style: write **pure functions**, let JAX handle optimization.  
- Famous trio:  
  - `grad` → automatic differentiation.  
  - `jit` → Just-In-Time compile with XLA (huge speedups).  
  - `vmap` → vectorization for batch operations.  

### ✅ Pros  
- Extremely fast thanks to XLA JIT compilation.  
- Clean functional style → fewer side effects.  
- Easy scaling across GPUs/TPUs.  
- Favored in cutting-edge research (DeepMind, Google Brain).  

### ❌ Cons  
- Steeper learning curve (functional, not object-oriented).  
- Ecosystem still smaller (though Flax, Haiku, Optax help).  
- Limited out-of-the-box deployment pipelines.  


### 4.1 JIT, grad, vmap — the holy trinity
```python
import jax, jax.numpy as jnp
from jax import jit, grad, vmap

def init_params(key, d=10, h=64):
    k1, k2, k3, k4 = jax.random.split(key, 4)
    W1 = jax.random.normal(k1, (d, h)) * 0.01
    b1 = jnp.zeros((h,))
    W2 = jax.random.normal(k2, (h, 1)) * 0.01
    b2 = jnp.zeros((1,))
    return (W1, b1, W2, b2)

def forward(params, x):
    W1, b1, W2, b2 = params
    h = jnp.maximum(0, x @ W1 + b1)  # ReLU
    return jax.nn.sigmoid(h @ W2 + b2)

def bce(y_true, y_pred, eps=1e-7):
    y_pred = jnp.clip(y_pred, eps, 1 - eps)
    return -jnp.mean(y_true * jnp.log(y_pred) + (1 - y_true) * jnp.log(1 - y_pred))

def loss_fn(params, x, y):
    return bce(y, forward(params, x))

@jit
def sgd_step(params, x, y, lr=1e-2):
    grads = grad(loss_fn)(params, x, y)
    return jax.tree_util.tree_map(lambda p, g: p - lr * g, params, grads)

key = jax.random.PRNGKey(0)
params = init_params(key)
x = jax.random.normal(key, (32, 10))
y = (x.sum(axis=1, keepdims=True) > 0).astype(jnp.float32)

for _ in range(50):
    params = sgd_step(params, x, y)
```

### 4.2 Batch without writing loops: `vmap`
```python
# forward over batch: map forward(x) across leading dimension
batched_forward = vmap(lambda xi: forward(params, xi), in_axes=0, out_axes=0)
preds = batched_forward(x)  # shape (32, 1)
```

### 4.3 Multi-device training (pmap) — heads-up
```python
from jax import pmap
# pmap would shard the batch across devices; simplified sketch:
@jit
def loss_single(params, xb, yb): return loss_fn(params, xb, yb)

p_loss = pmap(loss_single, in_axes=(None, 0, 0))
# Supply xb, yb sharded along first dim per device
```

### 4.4 Flax mini-module (higher-level library)
```python
from flax import linen as nn
import optax

class MLP(nn.Module):
    @nn.compact
    def __call__(self, x):
        x = nn.Dense(64)(x); x = nn.relu(x)
        x = nn.Dense(1)(x)
        return nn.sigmoid(x)

model = MLP()
key = jax.random.PRNGKey(0)
x = jnp.ones((1, 10))
params = model.init(key, x)

tx = optax.adam(1e-3)
opt_state = tx.init(params)

@jit
def train_step(params, opt_state, xb, yb):
    def loss_p(p): return bce(yb, model.apply(p, xb))
    grads = grad(loss_p)(params)
    updates, opt_state = tx.update(grads, opt_state, params)
    params = optax.apply_updates(params, updates)
    return params, opt_state
```


---  

## 5. JIT Compiler Deep Dive 🖥️  

### What is JIT (Just-in-Time)?  
- Instead of interpreting Python ops step by step…  
- JAX traces the function, optimizes it, compiles to **XLA** → runs as optimized machine code.  

👉 Example:  

```python
import jax
import jax.numpy as jnp

@jax.jit
def matmul(a, b):
    return jnp.dot(a, b)

x = jnp.ones((1000, 1000))
y = jnp.ones((1000, 1000))

# First run: compile + run
# Next runs: pure compiled speed 🚀
z = matmul(x, y)
```  

⚡ JIT can speed up training loops by **10–100x** depending on workload.  

PyTorch: has TorchScript (similar idea, less mature than XLA).  
TensorFlow: uses XLA optionally (graph optimizations).  
JAX: **native JIT-first** mindset.  

---  

## 6. Multi-language Ecosystem 🌍  

| Framework   | Languages | Notes |
|-------------|-----------|-------|
| **PyTorch** | Python, C++, limited Java bindings | TorchScript allows exporting to C++. Good for C++ production, weak elsewhere. |
| **TensorFlow** | Python, C++, Java, Go, TypeScript (TF.js), Swift (legacy) | Strongest multi-language support. TF.js enables ML in browsers, TF Lite for mobile. |
| **JAX** | Python only (officially) | Relies heavily on Python ecosystem. Some experimental bridges to TPU/C++ via XLA. |  

### Special Mentions  
- **Java**: TensorFlow Java API (popular for enterprise). PyTorch Java bindings exist but less mature.  
- **Golang**: TensorFlow Go API (production use). PyTorch Go support is minimal.  
- **TypeScript**: TensorFlow.js = run ML in browser or Node.js. PyTorch has no strong TS equivalent.  
- **Elixir**: Nx + Axon libraries in Elixir inspired by JAX’s design (leveraging XLA backend).  

👉 If you want **multi-language ML** → TensorFlow is strongest.  
👉 If you want **pure Python research speed** → PyTorch/JAX win.  

---  

## 7. Text Diagram  

```
PyTorch: "I’m flexible & research-friendly."  
TensorFlow: "I scale across languages & production."  
JAX: "I’m functional, fast, and cutting-edge."  
```  

---  

## 8. Trade-offs Table  

| Framework   | Best For | Pros 💪 | Cons ⚠️ |
|-------------|----------|---------|---------|
| PyTorch     | Research, prototyping | Easy, Pythonic, large research community | Deployment weaker, less multi-language |
| TensorFlow  | Production & deployment | Cross-platform, cross-language, scalable | More complex, heavier |
| JAX         | Research, performance | JIT, grad, vmap, speed on TPUs/GPUs | Smaller ecosystem, Python-only |  

---  

## 9. Example Applications 🚀  

- PyTorch → Research papers, prototypes, HuggingFace models.  
- TensorFlow → Mobile deployment, large-scale ML systems.  
- JAX → Reinforcement learning, experimental ML research.  

---  

## 10. Recap 🎉  

- **PyTorch** = best for researchers, dynamic & intuitive.  
- **TensorFlow** = best for industry-scale production, multi-language & deployment.  
- **JAX** = best for high-performance research with JIT & functional purity.  

⚡ Together, they cover the entire ML lifecycle: research → prototyping → deployment → experimental optimization.  
