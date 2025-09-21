# ⚙️ AI Compilers & Optimization: Quantization, Pruning, Distillation   

## 1. Why AI Compilers & Optimization?  

Imagine trying to fit a **big couch 🛋️ into a tiny apartment**.  
- The couch = massive AI model.  
- The apartment = mobile device / edge GPU.  

👉 Without optimization, models are too slow, big, or expensive.  
👉 With compilers + optimizations, we **shrink + speed up** models while keeping accuracy.  

---  

## 2. AI Compilers 🔨  

AI compilers = special toolchains that **translate models into efficient code** for CPUs, GPUs, TPUs.  

Examples:  
- **ONNX Runtime**  
- **TVM**  
- **TensorRT (NVIDIA)**  
- **XLA (TensorFlow/JAX)**  

Pipeline:  

```
[Trained Model] → [Compiler IR] → [Optimizations: fuse ops, parallelize] → [Target Hardware Binary]
```  

---  

## 3. Quantization 🔢  

Quantization = reduce precision of numbers (weights/activations).  

- FP32 → FP16 → INT8 → sometimes binary.  
- Goal: **smaller model, faster inference, less memory**.  

```python
# PyTorch static quantization example
import torch
from torch.quantization import quantize_dynamic

model_fp32 = torch.load("model.pth")
model_int8 = quantize_dynamic(model_fp32, {torch.nn.Linear}, dtype=torch.qint8)
torch.save(model_int8, "model_quantized.pth")
```  

✅ Speedup on CPUs/edge devices.  
❌ Accuracy drop if aggressive.  

---  

## 4. Pruning ✂️  

Pruning = remove unnecessary weights or neurons.  

- **Unstructured pruning**: zero out small weights.  
- **Structured pruning**: remove whole neurons/filters.  

```python
# PyTorch pruning example
import torch.nn.utils.prune as prune

prune.random_unstructured(model.fc, name="weight", amount=0.3)
```  

✅ Shrinks model size, speeds inference.  
❌ Risk of accuracy loss.  

---  

## 5. Distillation 🔥  

Knowledge distillation = train a **smaller student model** to mimic a large **teacher model**.  

```
[Teacher Model Output (soft labels)]  
           ↓  
[Student Model learns to imitate teacher]  
```  

```python
# Distillation sketch
teacher.eval()
for x, y in dataloader:
    with torch.no_grad():
        soft_labels = teacher(x)
    student_loss = distill_loss(student(x), soft_labels)
    ...
```  

✅ Keeps performance while reducing size.  
❌ Needs training + teacher access.  

---  

## 6. Combined Workflow 🏗️  

Typical optimization pipeline:  

```
Train big model → Distill student → Prune weights → Quantize → Compile → Deploy
```  

---  

## 7. Side-by-Side Workflow Diagram 🖼️  

```
Quantization            Pruning                  Distillation
-------------           -------------            ---------------
FP32 Weights            Full Dense Model         Large Teacher Model
     ↓                        ↓                          ↓
Reduce Precision        Remove small weights      Train small Student Model
(FP16 / INT8)           or neurons (structured)   on Teacher's outputs
     ↓                        ↓                          ↓
Smaller + Faster        Smaller + Faster          Smaller + Faster
But some accuracy drop  Accuracy may drop          Accuracy mostly preserved
```  

---  

## 8. Tools & Frameworks 🛠️  

- **Quantization**: PyTorch, TensorFlow Lite, ONNX Runtime.  
- **Pruning**: PyTorch, SparseML.  
- **Distillation**: HuggingFace Transformers, DistilBERT, TinyBERT.  
- **Compilers**: TVM, TensorRT, XLA.  

---  

## 9. Comparison Table 🆚  

| Technique       | Size Reduction 📦 | Speed Gain ⚡ | Accuracy Impact 🎯 | Notes |
|-----------------|------------------|--------------|--------------------|-------|
| **Quantization** | 2–4x smaller     | 2–4x faster  | Small to medium drop | Great for edge/CPU inference |
| **Pruning**     | 2–10x smaller    | 2–5x faster  | Medium drop if aggressive | Structured pruning helps hardware efficiency |
| **Distillation** | 2–5x smaller     | 2–3x faster  | Minimal drop (sometimes better) | Requires retraining with teacher model |  

---  

## 10. Case Study: DistilBERT 🧪  

DistilBERT is a **distilled version of BERT**:  

- Teacher = BERT (110M parameters).  
- Student = DistilBERT (66M parameters).  
- Training method = **knowledge distillation**.  

### Key Results  
- **40% smaller**.  
- **60% faster inference**.  
- **Retains ~97% of BERT’s performance** on NLP benchmarks.  

### How it Worked  
1. Train student on BERT’s soft labels (logits).  
2. Add loss from ground-truth labels.  
3. Use temperature scaling for smoother probabilities.  
4. Optimize trade-off: accuracy vs efficiency.  

```
DistilBERT = Student Model  
   ↓  
Trained with loss = α * CE(hard labels) + β * KL(soft labels from BERT)
```  

👉 Proof that distillation can shrink models massively **without big accuracy loss**.  

---  

## 11. Game Time 🎲  

Q1: You want to deploy a BERT model on mobile. Which techniques first?  
👉 Quantization + Distillation.  

Q2: You need to reduce GPU memory for inference by 50%. Which technique?  
👉 Quantization or pruning.  

Q3: You want a small chatbot model trained from GPT-3. Which method?  
👉 Distillation.  

Q4: What technique created DistilBERT?  
👉 **Knowledge Distillation**.  

---  

## 12. Recap 🎉  

- **Compilers** → map models to efficient hardware code.  
- **Quantization** → lower precision, smaller + faster.  
- **Pruning** → cut unnecessary weights.  
- **Distillation** → small model learns from big one.  
- **Workflow diagram** shows side-by-side differences.  
- **Case study (DistilBERT)** = real-world proof of distillation success.  

⚡ Optimization = making AI **practical + efficient**.  
