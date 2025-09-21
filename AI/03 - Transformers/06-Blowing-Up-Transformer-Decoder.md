# 🎬 Blowing Up the Transformer Decoder  
*"How the decoder turns context into translated words, one step at a time."*  

---

## 🎯 What’s the Decoder’s Job?  

Take:  
- Context-rich vectors from the **Encoder** (English sentence meaning).  
- A **Start token** in the target language (e.g., Kannada).  

Output:  
- A translated sentence, generated word by word.  

```
Encoder Output (English) ──▶ Decoder ──▶ Kannada Translation
```

---

## 🛠 Step 1: Input Preparation  

Target sentence prep (during training):  
- Add **Start token**, **End token**, and **Padding**.  
- Shape: **Batch × Max Length × 512**  

```
Batch: many sentences at once (e.g., 32)  
Max Length: fixed size (e.g., 20 tokens)  
512: word vector dimension  
```

### Positional Encoding  
Since inputs are parallel, we add sine/cosine **position signals** to word embeddings.  

```
Word Embedding (512) + Position Vector (512) = Position-Aware Embedding
```

---

## 👀 Step 2: Masked Multi-Head Self-Attention  

First block = decoder attends to **itself** (the partially generated sentence).  

1. **Q, K, V mapping**  
```
Each word → Q, K, V (512-dim)
Q = What am I looking for?
K = What info do I have?
V = What info I give
```

2. **Split into 8 heads**  
```
512 → 8 heads → 64-dim each
```

3. **Masking trick (to avoid cheating)**  
```
Lower-triangular mask:
Word i → can only see words ≤ i
Future words → -∞ (ignored)
```

4. **Attention math**  
```
Attention = softmax( (Q·Kᵀ)/√d ) × V
```

Result = **8 heads of context-aware vectors** → concatenated → 512-dim.  

---

## 🔗 Step 3: Add & Norm #1  

```
Attention Output + Original Input → Residual Connection
→ Layer Normalization (mean=0, std≈1)
```

Keeps gradients flowing + stabilizes training.  

---

## 🌍 Step 4: Cross-Attention (Link to Encoder)  

Now the decoder looks at the **encoder’s output (source sentence)**.  

- **Q** = from decoder (so far).  
- **K, V** = from encoder (full English sentence).  
- **No masking here!** Decoder can see the **whole source sentence**.  

```
Target word ↔ All source words
```

Output → Add & Norm again.  

---

## 🔧 Step 5: Feed-Forward Network (FFN)  

```
512 → Linear (expand to 1024) → ReLU + Dropout → Compress back to 512
```

- Learns nonlinear combos.  
- Add residual + LayerNorm.  

---

## 🔄 Step 6: Repeat N Times  

- One decoder block = Masked Self-Attention + Cross-Attention + FFN.  
- Stack it **6–12 times** for real tasks.  

Diagram:  
```
[Decoder Input]
   │
┌──▼──┐
│ Block │ × N
└──▼──┘
   │
[Context-rich decoder vectors]
```

---

## 🏁 Step 7: Final Output  

- 512-dim decoder vectors → Linear layer → Vocabulary size.  
- Softmax → probability distribution for next word.  

---

## 🧪 Training vs Inference  

### Training  
- All tokens processed in **parallel**.  
- Fast loss calculation vs ground-truth translation.  

### Inference (generation)  
- **One word at a time:**  
```
Start → Predict "nanna" → Feed back → Predict "hesaru" → ...
```

- Repeats until **End token**.  

---

## 📝 Key Takeaways  

1. Decoder = **Start token in, words out.**  
2. **Masked Self-Attention:** Prevents peeking at future words.  
3. **Cross-Attention:** Decoder uses encoder’s context.  
4. **FFN + Add & Norm:** Stabilize, refine, repeat.  
5. **Training = parallel**, **Inference = sequential.**  

---

💡 Memory Hook:  
**Decoder = storyteller:**  
- Remembers what it already said (masked self-attention).  
- Listens to the full source story (cross-attention).  
- Tells the translation, one word at a time.  
