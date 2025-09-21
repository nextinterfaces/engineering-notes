# 🧠 Self-Attention in Transformers  
*"How models stopped reading one word at a time and learned to look everywhere at once."*  

---

Insipired by https://www.youtube.com/watch?v=QCJQG4DuHT0&list=PLTl9hO2Oobd97qfWC40gOSU8C0iu0m2l4

## 😵 The Old Days: RNNs and Their Struggles  

RNNs were once the go-to for sequences (like sentences).  
But they came with two big problems:  

1. **Slowness** 🐢  
   - Words processed one by one.  
   - Training uses truncated backpropagation through time → slow & painful.  

2. **Context Limits** 🎯  
   - A word’s meaning depends on both left & right context.  
   - RNNs mostly looked **one way** (past → future).  
   - Even bi-RNNs split context into two halves (L2R + R2L) → not true holistic meaning.  

👉 Result: Weak representations, long training.  

---

## 💡 The Fix: Attention (and Self-Attention)  

Attention lets each word **look at every other word** in the sequence.  
When it’s applied to the same input sentence → **Self-Attention**.  

```
Word_i → looks at {Word_1, Word_2, ..., Word_n}
```

Now every word representation = context-rich, not isolated.  

---

## 🚀 Transformers: Attention at the Core  

Transformers fixed RNN problems:  

- ⚡ Process inputs in parallel → GPU-friendly.  
- 🔄 Use **multi-headed self-attention** to capture full context.  

### Architecture (Sequence-to-Sequence)  

```
Input Sentence ("my name is aj")
        │
        ▼
┌─────────────┐
│   Encoder   │  ← all words processed together
└─────┬───────┘
      │  context-rich vectors
      ▼
┌─────────────┐
│   Decoder   │  ← generates outputs step by step
└─────────────┘
      │
      ▼
Output: "ನನ್ನ ಹೆಸರು ಅಜ್" (Kannada)
```

---

## 🔍 Self-Attention: The Intuition  

For each word, the model makes 3 vectors:  

- **Query (Q):** What am I looking for?  
- **Key (K):** What info do I have?  
- **Value (V):** What info I actually pass along.  

```
Word → [Q, K, V]
```

### Step-by-Step  

1. **Affinity (Q·Kᵀ):** Compare every word with every other word.  
   → builds an attention matrix.  

2. **Scaling:** Divide by √d (dimension size) → stabilize values.  

3. **Masking (Decoder only):** Prevents “cheating” by looking ahead.  
   - Future words masked with -∞ above diagonal.  

4. **Softmax:** Convert to probabilities (rows sum to 1).  

5. **Context Vectors:** Multiply probs × Value matrix (V).  
   - Produces new vectors with richer context.  

---

## 🎭 Multi-Head Attention  

One head = one perspective.  
But meaning is multi-dimensional → we use **multiple heads**.  

```
Head 1: focus on syntax  
Head 2: focus on subject-object relations  
Head 3: focus on long-range dependencies  
...  
Stack them → Multi-Head Attention
```

---

## 📝 Key Takeaways  

1. RNNs = slow, limited context.  
2. Attention = every word looks at every other word.  
3. Self-Attention = applied to the same sentence.  
4. Transformer = parallel + multi-head self-attention.  
5. Multi-Head = many “views” of relationships combined.  

---

💡 Memory Hook:  
**RNNs = single-file reading line by line.  
Transformers = everyone in class looking at everyone else’s notes at once.**  
