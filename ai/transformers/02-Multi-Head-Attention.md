# 🎭 Multi-Head Attention in Transformers  
*"Why one pair of eyes isn’t enough — models need many perspectives."*  

---

## 🚀 Transformer Recap (Sequence-to-Sequence)  

Transformers = **Encoder + Decoder** for sequence-to-sequence tasks.  

Example: Translate `"my name is aj"` → Hindi.  

```
Input: "my name is aj"
        │
        ▼
┌─────────────┐
│   Encoder   │  (all words processed at once)
└─────┬───────┘
      │ context-rich vectors
      ▼
┌─────────────┐
│   Decoder   │  (generates step by step: "Mera" → "Nam" → "aj" → "hey")
└─────────────┘
```

---

## 🧠 The Core Idea: Multi-Head Attention  

Each word vector → split into 3 roles:  

- **Query (Q):** What I’m looking for  
- **Key (K):** What I can offer  
- **Value (V):** What I actually deliver  

```
Word → [Q, K, V]
```

But one "attention head" may miss relationships.  
👉 Solution: **multiple heads**, each with a different perspective.  

---

## 🧩 Splitting into Heads  

Example: Input vector = 512 dimensions.  

- Use **8 heads**.  
- Split 512 → 8 × 64.  
- Each head gets its own Q, K, V of size 64.  

```
Q (512) → [Q1(64), Q2(64), … Q8(64)]
K (512) → [K1(64), … K8(64)]
V (512) → [V1(64), … V8(64)]
```

Each head runs attention separately → produces its own 64-dim output per word.  

---

## 🔎 Inside One Attention Head  

Steps for each head:  

1. **Scores:** `Q · Kᵀ` → affinity between words. (shape: Seq × Seq)  
2. **Scale:** Divide by √64 = 8 → keep stable.  
3. **Mask (Decoder only):** Prevent peeking at future words → fill upper-triangle with -∞.  
4. **Softmax:** Convert scores → probabilities (rows sum = 1).  
5. **Weighted Sum:** Multiply probs × V → new context-rich word vectors.  

```
Attention = softmax( (Q·Kᵀ) / √d ) × V
```

---

## 🎭 Multi-Head Magic  

Each head learns different relationships:  

```
Head 1: Subject ↔ Verb
Head 2: Long-distance dependencies
Head 3: Word order nuances
...
Head 8: Subtle context
```

Then:  

```
[Head1(64), Head2(64), … Head8(64)] → Concatenate → 512-dim vector
```

Finally pass through a linear layer → fuse information.  

---

## 📚 PyTorch Implementation (Conceptual Flow)  

```
X: [Batch=1, Seq=4, Dim=512]

Step 1: Linear map → QKV (1536 dims)
Step 2: Reshape → (Heads=8, Seq=4, Q/K/V=64)
Step 3: Compute Q·Kᵀ for each head (4×4 matrix)
Step 4: Scale by √64
Step 5: Mask (if decoder)
Step 6: Softmax
Step 7: Multiply by V → context vectors (Seq × 64)
Step 8: Concatenate heads → (Seq × 512)
Step 9: Final linear (512×512)
```

---

## 📝 Key Takeaways  

1. Multi-head = multiple perspectives → richer context.  
2. Each head = runs self-attention independently.  
3. Outputs are concatenated + transformed.  
4. Encoder & Decoder both rely on multi-head attention.  

---

💡 Memory Hook:  
**One head = one spotlight.  
Multi-head = concert lighting from every angle.**  
