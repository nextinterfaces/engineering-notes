# 🚀 Retrieval Augmented Generation (RAG)  
*"How to stop your LLM from confidently lying to you."*  

---

## 😱 The Problem: Hallucinations  

LLMs are like **super-confident students**. They’ll answer every question with a smile…  
…but sometimes they make things up.  

Example:  
**Q:** What’s the closest planet to Earth?  
**LLM:** "Obviously Mars!" ❌  
(Reality check: it’s Venus ✅)  

We need a framework to keep them grounded.  
Enter: **RAG**.  

---

## 🧩 What is RAG?  

Think of RAG as a **team-up**:  

```
┌─────────────┐   pulls facts   ┌───────────────┐
│   Retriever │ ──────────────▶ │   Generator   │
└─────────────┘                 └───────────────┘
      (librarian)                     (writer)
```

🎯 Goal: More **accurate, up-to-date, relevant** answers.  

---

## 🏗️ Naive RAG Pipeline  

### Indexing (before a question is asked)  
```
Raw Data → [Chunker] → [Vectorizer] → [Index]
```

- **Chunker:** Split text into smaller chunks (sentence, sliding window).  
- **Vectorizer:** Convert chunks → embeddings (vectors).  
- **Index:** Store vectors in embedding space (similar meanings close).  

### Retrieval + Generation (when asked)  
```
Question → [Vectorize] → [Find nearest chunks] → [Augment prompt] → [LLM generates answer]
```

---

## ⚡ Advanced RAG (Better Pipeline)  

Naive RAG can fail if retrieval = bad.  
Advanced RAG adds **pre- and post-retrieval steps**:  

```
[User Query]
     │
     ▼
 Pre-Retrieval (rewrite, expand, route)
     │
     ▼
 Retrieval from Index
     │
     ▼
 Post-Retrieval (rerank, summarize, fuse)
     │
     ▼
 LLM generates grounded answer
```

---

## 🧱 Modular RAG (LEGO-style)  

Instead of one fixed pipeline → **modular building blocks**.  

```
[Chunker] → [Indexer] → [Retriever] → [Reranker] → [Summarizer] → [LLM]
```

- Swap modules depending on task.  
- Easier to maintain + upgrade.  
- Efficient: light queries use fewer steps, complex queries use more.  

---

## 💡 Memory Hooks  

- RAG = Librarian + Writer.  
- Naive = simple cooking recipe.  
- Advanced = recipe with ingredient check + taste test.  
- Modular = LEGO AI blocks.  
