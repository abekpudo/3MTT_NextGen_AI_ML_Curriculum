 Semantic Search: Searching by Meaning

 1. Overview
Traditional search (like Google in the 90s) uses Keywords. If you search for "feline," it won't find pages that only use the word "cat." Semantic Search uses embeddings to understand the concept behind the words, allowing it to find relevant results even if there are no matching keywords.

 2. The Core Mechanics

 2.1. Cosine Similarity
To find how similar two sentences are, we calculate the angle between their embedding vectors.
- Score of 1: Identical meaning.
- Score of 0: Completely unrelated.
- Score of -1: Opposite meaning.

 2.2. The Bi-Encoder Architecture
The standard for fast retrieval.
1. Encode all your documents into vectors and store them in a Vector Database.
2. When a user types a query, encode the query into a vector.
3. Use Approximate Nearest Neighbors (ANN) to find the closest document vectors in milliseconds.

 3. Vector Databases
Since comparing a query to millions of documents is slow, we use specialized databases:
- FAISS (Facebook AI Similarity Search): A library for efficient similarity search.
- ChromaDB / Pinecone / Milvus: Managed databases that store both the vector and the original text.

 4. Re-Ranking with Cross-Encoders
Bi-Encoders are fast but sometimes lose nuance. For high-precision search:
1. Use a Bi-Encoder to find the top 100 results (Fast).
2. Use a Cross-Encoder to score those 100 results more accurately (Slow but very smart).
3. Re-order the results based on the Cross-Encoder scores.

 5. Hands-On: Simple Retrieval with FAISS
```python
import numpy as np
import faiss
from sentence_transformers import SentenceTransformer

 1. Initialize model and data
model = SentenceTransformer('all-MiniLM-L6-v2')
documents = ["Artificial intelligence is the future", "I love eating spicy food", "The weather is nice today"]
doc_embeddings = model.encode(documents)

 2. Build FAISS Index
dimension = doc_embeddings.shape[1]
index = faiss.IndexFlatL2(dimension)
index.add(doc_embeddings.astype('float32'))

 3. Query
query = "Tell me about AI"
query_embedding = model.encode([query])

 4. Search
D, I = index.search(query_embedding.astype('float32'), k=1)
print(f"Top Result: {documents[I[0][0]]}")
```

 6. Resources
- [Sentence Transformers: Semantic Search Guide](https://www.sbert.net/examples/applications/semantic-search/README.html)
- [Pinecone: What is a Vector Database?](https://www.pinecone.io/learn/vector-database/)

---
