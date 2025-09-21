# 🧠 Index Types in Vector Databases

Imagine you're a **librarian of infinite bookshelves**. Each book is a
vector (say, a 768-dimensional embedding of a sentence). Someone runs up
to you shouting:

> "Quick! Find me the books most similar to THIS one!"

Now, how you organize your shelves makes the difference between
**finding it in 0.01s** vs **spending a lifetime scanning every book.**

That's where **indexes** come in.

------------------------------------------------------------------------

## 🗂️ Index Type 1: The FLAT Index

Think: **brute force bodybuilder** 💪

    Query -----> [Check EVERY vector one by one]
                 -------------------------------
                 |  📕  📗  📘  📙  📓  📔 ...  |
                 -------------------------------

-   Stores all vectors in raw form.
-   On query: compute distance (cosine/Euclidean) to **all vectors**.
-   100% accurate, but sloooow when you have millions of vectors.\
-   Great baseline. Simple. Perfect recall.

👉 Use when dataset is small or accuracy matters more than speed.

------------------------------------------------------------------------

## 🗂️ Index Type 2: KNN Graphs (e.g., HNSW)

Think: **social network of vectors** 🌐

    Levels of graph:

    Level 3:     [A]---[B]---[C]
                  |           |
    Level 2:   [D]---[E]---[F]---[G]
                  \         /
    Level 1:   [H]---[I]---[J]---[K]

    Query enters at top → walks graph → zooms down.

-   HNSW = **Hierarchical Navigable Small World**.
-   Build a **multi-layer graph**: higher levels = fewer, long-range
    "express links"; lower levels = dense local neighborhoods.\
-   Search: start at top, follow closest neighbors, "zoom down" until
    you land near your query vector.
-   🔥 Extremely fast and very accurate.
-   Cost: more memory, complex building.

👉 This is the **rockstar** ANN index used in **Pinecone, Weaviate,
Redis, FAISS, Milvus, Vespa**.

------------------------------------------------------------------------

## 🗂️ Index Type 3: IVF (Inverted File Index)

Think: **postal zones for vectors** 🏙️

    Step 1: Divide space into "clusters" (like neighborhoods)
            [Cluster 1] [Cluster 2] [Cluster 3] ...

    Step 2: Only search in a FEW clusters nearest to the query

-   K-means partitions vectors into clusters.
-   Store vectors in these inverted lists.
-   On query: figure out which clusters query falls into → only search
    inside those.
-   Much faster than flat, but recall drops if wrong cluster is chosen.

👉 Best for **billions of vectors**.

------------------------------------------------------------------------

## 🗂️ Index Type 4: PQ / Product Quantization

Think: **lossy compression for speed demons** 🚀

    Vector (128D) → Split into chunks → Encode each chunk
       [X1 X2 X3 ...] → [codebook1: id=7] [codebook2: id=3] ...

-   Compresses vectors into small codes (like vector "zip files").
-   Distances computed approximately (via codebooks).
-   Huge memory savings (GB → MB).
-   Accuracy lower, but often "good enough."

👉 Use for large-scale, low-memory environments.

------------------------------------------------------------------------

## 🗂️ Mix and Match

Real systems combine them:

-   **IVF + PQ** → Clustered + compressed.\
-   **HNSW + PQ** → Fast graph + compressed storage.\
-   **Flat + GPU** → Brute force but blazing on GPUs.

------------------------------------------------------------------------

# 🎯 Summary Cheat-Sheet

  Index   Speed           Accuracy     Memory        Best Use
  ------- --------------- ------------ ------------- --------------------
  Flat    ❌ Slow         ✅ Perfect   🟢 Moderate   Small datasets
  HNSW    ✅ Fast         ✅ High      🔴 Higher     Most popular ANN
  IVF     ✅ Fast         ⚠️ Medium    🟢 Lower      Huge datasets
  PQ      ✅ Super fast   ⚠️ Lower     ✅ Tiny       Memory constrained

------------------------------------------------------------------------

# 🎨 Mental Picture

    Flat  = Walking every aisle in the library
    HNSW  = Using a VIP guide with shortcuts
    IVF   = Going straight to the right neighborhood
    PQ    = Looking at “summaries” instead of full books
