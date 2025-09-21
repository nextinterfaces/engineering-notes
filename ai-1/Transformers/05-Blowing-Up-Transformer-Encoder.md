# 💥 Blowing Up the Transformer Encoder
*"Turning words into context-rich vectors, step by step."*  

---

## 🎯 The Big Goal  

The **Encoder** takes a sentence → outputs **context-rich vectors**.  
These vectors = “what each word means in context.”  

Example:  
```
Input:  "my name is aj"
Output: 4 vectors (one per word), each 512-dim, context-aware
```

Why context matters:  
- “Aj” in *“my name is aj”* vs. in *“aj is programming”* → same word, different meaning in context.  

---

## 🛠 Step 1: Input Preparation  

The input sentence has to be reshaped into something the network understands.  

### a) Tokenization + Padding  
```
Sentence → ["my", "name", "is", "aj"]
```
- Pad if needed: `["my", "name", "is", "aj", "<PAD>", "<PAD>"]` (for fixed length).  

### b) One-Hot Encoding  
```
"my"   = [0,0,0,1,0,0,...,0]  (vocab size ~50k)
"name" = [0,0,1,0,0,0,...,0]
```
👉 Very sparse and high-dimensional.  

### c) Embedding  
```
One-hot (50k) → Dense vector (512)
```
Learned matrix compresses to 512-dim vector:  
```
"my"   → [0.12, -0.43, ..., 0.56] (512-dim)
"name" → [0.88,  0.01, ..., 0.33]
```

### d) Positional Encoding  
Since Transformer = parallel (not sequential), it has no order awareness.  
So we **add sin/cos signals** to each embedding.  

```
Word Embedding + Position Signal = Position-Aware Embedding
```

Diagram:  
```
my(emb) + pos(0) → vector
name(emb) + pos(1) → vector
is(emb) + pos(2) → vector
aj(emb) + pos(3) → vector
```

---

## 🧠 Step 2: Multi-Head Self-Attention  

Each token embedding (512-dim) is split into three roles:  

- **Query (Q):** “What am I looking for?”  
- **Key (K):** “What info do I have?”  
- **Value (V):** “What info do I pass on?”  

```
Token → Q, K, V  (each 512-dim)
```

### Attention Matrix  
For each head:  
```
Attention = softmax( (Q · Kᵀ) / √d ) × V
```

- Q·Kᵀ = affinity between words.  
- Scale by √d → stabilize.  
- Softmax → convert to probabilities.  
- Multiply by V → weighted context.  

**Example:**  
```
Word = "name"
Q("name") · K("my")   → 0.7  → focus on "my"
Q("name") · K("is")   → 0.2  → less focus
Q("name") · K("aj")   → 0.1  → small focus
```
👉 “name” attends mostly to “my.”  

### Multi-Head Trick  
- 512-dim split into 8 heads (each 64-dim).  
- Each head attends differently (syntax, long-distance, subject-object).  
- Outputs concatenated back → 512-dim.  

Diagram:  
```
[Head1: 64] [Head2: 64] ... [Head8: 64] → Concatenate → 512
```

---

## 🔗 Step 3: Add & Norm #1  

```
Attention Output (512)
    +
Original Input (512)
    ↓
Residual Connection
    ↓
Layer Normalization
```

- Residual → prevents vanishing gradients, lets info skip.  
- LayerNorm → stabilize activations (mean=0, std≈1).  

---

## 🔧 Step 4: Feed-Forward + Add & Norm #2  

### Feed-Forward Network (FFN)  
```
Input (512)
 → Expand (512 → 1024)
 → ReLU + Dropout
 → Compress (1024 → 512)
```
👉 Learns more complex relationships beyond attention.  

### Add & Norm Again  
```
FFN output + previous → LayerNorm
```

Keeps everything stable, helps deep stacking.  

---

## 🔄 Step 5: Stacking Layers  

One encoder = all steps above.  
Transformers use **12–24 encoder blocks stacked**.  

Diagram:  
```
[Input Embeddings]
     │
┌────▼────┐
│ Encoder │ × 12
└────▼────┘
     │
[Context-rich vectors → Decoder]
```

---

## 📝 Key Takeaways  

1. **Embeddings + Position** = words with order.  
2. **Self-Attention** = each word looks at all others.  
3. **Multi-Head** = multiple perspectives.  
4. **Add & Norm** = skip connections + stable training.  
5. **FFN** = nonlinear mixing of features.  
6. **Stacked Encoders** = deeper, richer word understanding.  

---

💡 Memory Hook:  
**Encoder = language assembly line:**  
- Add meaning (embedding).  
- Add order (position).  
- Mix context (attention).  
- Refine (FFN).  
- Repeat (stack).  
