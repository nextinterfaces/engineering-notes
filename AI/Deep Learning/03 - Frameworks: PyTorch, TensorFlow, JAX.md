# ⚙️ Deep Learning Frameworks: PyTorch, TensorFlow & JAX  

## 1. Why Frameworks?  

Imagine building a house 🏠.  
- You **could** cut every brick by hand (raw NumPy).  
- Or use **construction tools** (frameworks).  

👉 PyTorch, TensorFlow, and JAX = power tools for deep learning.  

---  

## 2. PyTorch – The Researcher’s Best Friend 🧪  

- **Dynamic computation graph** (“define by run”).  
- Feels like Python → easy debugging.  
- Popular in academia & prototyping.  

### Example: Simple Neural Net in PyTorch  

```python
import torch
import torch.nn as nn
import torch.optim as optim

# Define model
class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.fc1 = nn.Linear(10, 5)
        self.fc2 = nn.Linear(5, 1)

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        return torch.sigmoid(self.fc2(x))

model = Net()
optimizer = optim.SGD(model.parameters(), lr=0.01)
loss_fn = nn.BCELoss()

# Forward + backward pass
x = torch.randn(1, 10)
y = torch.tensor([[1.0]])
y_pred = model(x)
loss = loss_fn(y_pred, y)
loss.backward()
optimizer.step()
```  

👉 Easy to read, debug, and modify.  

---  

## 3. TensorFlow – The Production Powerhouse 🏭  

- **Static computation graph** (TensorFlow 1.x).  
- TensorFlow 2.x = more PyTorch-like with eager execution.  
- Tight integration with **Keras API** → simple high-level models.  
- Great for **scalability & deployment (TF Serving, TF Lite, TPU support)**.  

### Example: Simple Neural Net in TensorFlow/Keras  

```python
import tensorflow as tf
from tensorflow.keras import layers

# Define model
model = tf.keras.Sequential([
    layers.Dense(5, activation="relu", input_shape=(10,)),
    layers.Dense(1, activation="sigmoid")
])

model.compile(optimizer="sgd", loss="binary_crossentropy")

# Forward + backward pass
import numpy as np
x = np.random.randn(1, 10)
y = np.array([[1.0]])
model.train_on_batch(x, y)
```  

👉 Cleaner for production pipelines & deployment.  

---  

## 4. JAX – The New Kid with Superpowers ⚡  

- From Google Research.  
- **NumPy on steroids**: automatic differentiation (`grad`), vectorization (`vmap`), compilation (`jit`).  
- Loved in cutting-edge ML (e.g., Flax, Haiku, DeepMind).  

### Example: Neural Net in JAX  

```python
import jax
import jax.numpy as jnp
from jax import grad, jit

# Simple forward function
def predict(params, x):
    w1, b1, w2, b2 = params
    h = jnp.maximum(0, jnp.dot(x, w1) + b1)  # ReLU
    return jax.nn.sigmoid(jnp.dot(h, w2) + b2)

# Loss function
def loss(params, x, y):
    preds = predict(params, x)
    return jnp.mean((preds - y) ** 2)

# Initialize params
key = jax.random.PRNGKey(0)
x = jax.random.normal(key, (10,))
y = jnp.array(1.0)
params = [
    jax.random.normal(key, (10, 5)), jnp.zeros(5),
    jax.random.normal(key, (5, 1)), jnp.zeros(1)
]

# Gradient descent step
grads = grad(loss)(params, x, y)
```  

👉 Blazing fast + functional style, perfect for researchers.  

---  

## 5. Text Diagram  

```
PyTorch: "I’m flexible & Pythonic. Great for researchers."  
TensorFlow: "I scale to production, from mobile to cloud."  
JAX: "I’m the math nerd. Super fast + elegant gradients."  
```  

---  

## 6. Trade-offs Table  

| Framework   | Best For | Strengths 💪 | Weaknesses ⚠️ |
|-------------|----------|--------------|---------------|
| PyTorch     | Research, prototyping | Easy to debug, dynamic graph | Less optimized for mobile/prod |
| TensorFlow  | Production, deployment | Scalable, TPU/edge support | Historically complex (TF 1.x) |
| JAX         | Cutting-edge research  | Auto-diff, jit, functional | Smaller ecosystem, steeper learning curve |  

---  

## 7. Game Time 🎲  

Q: Which framework would you choose for:  
- Prototyping a new NLP model? → PyTorch  
- Deploying to mobile at scale? → TensorFlow  
- Doing experimental research with custom gradients? → JAX  

---  

## 8. Recap 🎉  

- **PyTorch** = researcher’s playground.  
- **TensorFlow** = production-ready giant.  
- **JAX** = fast, functional, experimental engine.  

⚡ Together, they form the **holy trinity of deep learning frameworks**.  
