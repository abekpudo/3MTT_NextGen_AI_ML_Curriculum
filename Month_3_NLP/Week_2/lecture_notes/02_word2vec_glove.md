 Static Embeddings: Word2Vec and GloVe

 1. Overview
Before Transformers, NLP was revolutionized by Static Word Embeddings. These models learn to represent words as dense vectors based on the company they keep (the "Distributional Hypothesis").

 2. Word2Vec (Google, 2013)
Word2Vec is a neural network with one hidden layer that learns to predict words.

 2.1. CBOW (Continuous Bag of Words)
Predicts a target word based on its context (surrounding words).
- Use Case: Faster to train, slightly better accuracy for frequent words.

 2.2. Skip-gram
Predicts the context based on a single target word.
- Use Case: Works better with small amounts of training data and represents rare words well.

 3. GloVe (Stanford, 2014)
GloVe (Global Vectors for Word Representation) is an extension of Word2Vec.
- How it works: It uses a "Global Co-occurrence Matrix" to count how often every word appears with every other word in the entire dataset.
- Key Insight: It combines the local context of Word2Vec with the global statistics of matrix factorization (like PCA).

 4. Vector Arithmetic
One of the most famous properties of these embeddings is their ability to capture analogies:
- `Vector("King") - Vector("Man") + Vector("Woman") ≈ Vector("Queen")`
- `Vector("Nigeria") - Vector("Abuja") + Vector("Paris") ≈ Vector("France")`

 5. Limitations of Static Embeddings
1. Polysemy: A single vector cannot represent multiple meanings (e.g., "Bank" as a financial institution vs. a river side).
2. Out of Vocabulary (OOV): If a word wasn't in the training set, it has no vector.

 6. Hands-On with Gensim
```python
import gensim.downloader as api

 Load pretrained Word2Vec (this might take a moment)
model = api.load("word2vec-google-news-300")

 1. Find similar words
print(model.most_similar("data", topn=5))

 2. Perform vector math (King - Man + Woman)
result = model.most_similar(positive=['king', 'woman'], negative=['man'], topn=1)
print(f"King - Man + Woman = {result}")

 3. Check similarity
similarity = model.similarity('computer', 'laptop')
print(f"Similarity: {similarity:.4f}")
```

 7. Resources
- [Gensim Documentation](https://radimrehurek.com/gensim/auto_examples/index.html)
- [Projector.tensorflow.org](https://projector.tensorflow.org/) (Visualize embeddings in 3D!)

---
