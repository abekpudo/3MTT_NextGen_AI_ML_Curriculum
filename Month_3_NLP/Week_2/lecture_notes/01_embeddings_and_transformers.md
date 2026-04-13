 Embeddings & Transformers: The Architecture of Modern NLP

 1. Overview
Traditional NLP relied on simple counts. Modern NLP relies on dense vectors (embeddings) and parallel architectures (Transformers). This lecture explores how we went from simple word vectors to models that understand context and nuance.

 2. Word Embeddings (The Foundation)
A word embedding is a numerical representation of a word where similar words have similar vectors.
- Static Embeddings: Every word has one fixed vector (e.g., "bank" in "river bank" and "bank account" have the same vector).
- Contextual Embeddings: The vector changes based on surrounding words (e.g., the vector for "bank" changes based on context).

 3. The Transformer Architecture
Introduced in 2017 ("Attention is All You Need"), the Transformer replaced sequential processing (RNNs) with Self-Attention.

 3.1. Self-Attention
Allows the model to look at every other word in a sentence to decide which are most relevant to the current word.
- Example: In "The animal didn't cross the street because it was too tired," self-attention helps the model link "it" to "animal."

 3.2. Multi-Head Attention
Instead of one "opinion" on relevance, the model uses multiple "heads" to look at different relationships simultaneously (e.g., one head for grammar, one for meaning).

 3.3. Positional Encoding
Since Transformers process all words at once (in parallel), they need a way to know the order of words. Positional encodings are added to the embeddings to provide this "spatial" information.

 4. Transfer Learning in NLP
The "Transformer Revolution" allowed for:
1. Pretraining: Train a massive model on billions of words.
2. Fine-Tuning: Take that model and "tweak" it for a specific task (like sentiment analysis) with very little data.

 5. Hands-On: Using Sentence Transformers
```python
from sentence_transformers import SentenceTransformer, util

 Load a pretrained model
model = SentenceTransformer('all-MiniLM-L6-v2')

sentences = ["The cat sits outside", "A man is playing guitar", "The new movie is awesome"]

 Compute embeddings
embeddings = model.encode(sentences)

 Compute similarity
cosine_scores = util.cos_sim(embeddings[0], embeddings[1])
print(f"Similarity score: {cosine_scores.item():.4f}")
```

 6. Resources
- [The Illustrated Transformer (Jay Alammar)](https://jalammar.github.io/illustrated-transformer/)
- [Hugging Face Course: How Transformers Work](https://huggingface.co/learn/nlp-course/chapter1/1)

---
