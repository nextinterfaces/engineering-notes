# ⚖️ Layer Normalization in Transformers  
*"Keeping activations calm so Transformers can learn fast."*  

---

## 🚀 Context: Where LayerNorm Fits  

Transformers = Encoder + Decoder.  
Inside each block, after Multi-Head Attention → we see **Add & Norm**.  

```
Input → Embedding → Positional Encoding → Multi-Head Attention
         │                                   │
         └────── Residual Connection ────────┘
                         │
                   [ Add & Norm ]
```

---

## 🔗 Add (Residual Connections)  

- Take the **input before attention (X')** and **add it** to the attention output.  
- Purpose: keep information flowing, prevent vanishing gradients.  

```
Output = Attention(X') + X'
```

👉 Residuals = skip shortcuts so deep nets don’t “forget” signals.  

---

## ⚖️ Norm (Layer Normalization)  

Neural nets produce activations with wild ranges → unstable training.  
Normalization = bring them to a calm, stable range.  

### Formula  

```
y = γ * ( (x - μ) / σ ) + β
```

- μ = mean of activations (per layer, per word).  
- σ = standard deviation.  
- γ, β = learnable scale & shift parameters.  

👉 After normalization → mean ≈ 0, std ≈ 1.  

---

## 🧮 Example  

Word embedding dims = [0.2, 0.1, 0.3]  

- μ = (0.2+0.1+0.3)/3 = 0.2  
- σ = std([0.2,0.1,0.3])  
- Normalize each → (x - μ)/σ  
- Add ε in denominator to avoid divide-by-zero.  
- Scale + shift with γ, β.  

Result = stable, normalized vector.  

---

## 📊 Why It Matters  

- ⚡ Faster training (no exploding/vanishing activations).  
- 🎯 More stable gradient updates.  
- 🔄 Works across each word vector in the sequence.  

---

## 📝 Key Takeaways  

1. **Add & Norm** = Residual connection + LayerNorm.  
2. Residual keeps info flow alive.  
3. LayerNorm stabilizes training by normalizing activations.  
4. γ & β = learnable tweaks for flexibility.  
5. Without LayerNorm → Transformers would struggle to converge.  

---

💡 Memory Hook:  
**Residuals = shortcuts.  
LayerNorm = meditation for neurons (stay calm, balanced).**  
