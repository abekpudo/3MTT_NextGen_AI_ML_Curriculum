 Exercise 4: Semantic Analysis Applications

 🎯 Objective
Build practical applications using word embeddings and semantic understanding.

---

 📋 Tasks

 Task 1: Semantic Search Engine
Build a semantic search system using embeddings.

```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

class SemanticSearch:
    """
    Semantic search engine using document embeddings.
    """
    def __init__(self, embedding_model):
        """
        Initialize with embedding model.
        
        Args:
            embedding_model: Function that returns embedding for text
        """
        self.embedding_model = embedding_model
        self.documents = []
        self.embeddings = None
    
    def index_documents(self, documents: list):
        """
        Index documents for search.
        
        Args:
            documents (list): List of document strings
        """
        self.documents = documents
        self.embeddings = np.array([self.embedding_model(doc) for doc in documents])
    
    def search(self, query: str, top_k: int = 5) -> list:
        """
        Search for similar documents.
        
        Args:
            query (str): Search query
            top_k (int): Number of results to return
            
        Returns:
            list: List of (document, score) tuples
        """
        if self.embeddings is None:
            raise ValueError("No documents indexed. Call index_documents first.")
        
         Get query embedding
        query_embedding = self.embedding_model(query)
        
         Calculate similarities
        similarities = cosine_similarity([query_embedding], self.embeddings)[0]
        
         Get top K results
        top_indices = similarities.argsort()[-top_k:][::-1]
        
        results = [(self.documents[i], similarities[i]) for i in top_indices]
        
        return results

 Simple embedding function (replace with real embeddings)
def simple_embedding(text: str) -> np.ndarray:
    """
    Simple embedding function (for demo - replace with real embeddings).
    
    Args:
        text (str): Input text
        
    Returns:
        array: Embedding vector
    """
     This is a placeholder - use word2vec, BERT, etc.
    words = text.lower().split()
     Create simple character-based embedding
    embedding = np.zeros(100)
    for i, char in enumerate(' '.join(words)):
        embedding[i % 100] = ord(char)
    return embedding

 Test
search_engine = SemanticSearch(simple_embedding)

documents = [
    "Natural language processing is a field of artificial intelligence",
    "Machine learning enables computers to learn from data",
    "Deep learning uses neural networks for complex tasks",
    "Computer vision allows machines to understand images",
    "Reinforcement learning trains agents through rewards",
]

search_engine.index_documents(documents)

query = "artificial intelligence and neural networks"
results = search_engine.search(query, top_k=3)

print(f"Query: {query}\n")
print("Search Results:")
for doc, score in results:
    print(f"  Score: {score:.3f} | {doc}")
```

---

 Task 2: Document Clustering
Cluster documents using embeddings.

```python
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt

class DocumentClusterer:
    """
    Document clustering using embeddings.
    """
    def __init__(self, embedding_model, n_clusters=3):
        """
        Initialize clusterer.
        
        Args:
            embedding_model: Function that returns embedding
            n_clusters (int): Number of clusters
        """
        self.embedding_model = embedding_model
        self.n_clusters = n_clusters
        self.kmeans = None
        self.pca = None
    
    def fit(self, documents: list):
        """
        Fit clustering model.
        
        Args:
            documents (list): List of document strings
        """
         Get embeddings
        embeddings = np.array([self.embedding_model(doc) for doc in documents])
        
         Cluster
        self.kmeans = KMeans(n_clusters=self.n_clusters, random_state=42)
        self.kmeans.fit(embeddings)
        
         Fit PCA for visualization
        self.pca = PCA(n_components=2)
        self.pca.fit(embeddings)
        
        return self.kmeans.labels_
    
    def predict(self, documents: list):
        """
        Predict cluster for documents.
        
        Args:
            documents (list): List of document strings
            
        Returns:
            array: Cluster labels
        """
        embeddings = np.array([self.embedding_model(doc) for doc in documents])
        return self.kmeans.predict(embeddings)
    
    def visualize(self, documents: list, labels=None):
        """
        Visualize clusters.
        
        Args:
            documents (list): List of documents
            labels (list): Optional labels for coloring
        """
        embeddings = np.array([self.embedding_model(doc) for doc in documents])
        
         Reduce to 2D
        embeddings_2d = self.pca.transform(embeddings)
        
         Plot
        plt.figure(figsize=(10, 8))
        
        if labels is None:
            labels = self.kmeans.labels_
        
        scatter = plt.scatter(embeddings_2d[:, 0], embeddings_2d[:, 1], c=labels, cmap='viridis')
        plt.colorbar(scatter)
        plt.xlabel('PCA 1')
        plt.ylabel('PCA 2')
        plt.title('Document Clusters')
        plt.show()

 Test
clusterer = DocumentClusterer(simple_embedding, n_clusters=2)

documents = [
    "AI and machine learning are transforming technology",
    "Neural networks are powerful tools for deep learning",
    "The stock market showed significant gains today",
    "Investors are optimistic about tech stocks",
    "Natural language processing enables chatbots",
    "Financial markets react to economic indicators",
]

labels = clusterer.fit(documents)
print(f"Cluster labels: {labels}")

 Visualize (requires matplotlib)
 clusterer.visualize(documents)
```

---

 Task 3: Document Similarity Calculator
Calculate similarity between documents.

```python
def document_similarity(doc1: str, doc2: str, embedding_model) -> float:
    """
    Calculate similarity between two documents.
    
    Args:
        doc1 (str): First document
        doc2 (str): Second document
        embedding_model: Embedding function
        
    Returns:
        float: Similarity score
    """
    emb1 = embedding_model(doc1)
    emb2 = embedding_model(doc2)
    
    return cosine_similarity([emb1], [emb2])[0][0]

def document_similarity_matrix(documents: list, embedding_model) -> np.ndarray:
    """
    Calculate pairwise similarity matrix.
    
    Args:
        documents (list): List of documents
        embedding_model: Embedding function
        
    Returns:
        array: Similarity matrix
    """
    embeddings = np.array([embedding_model(doc) for doc in documents])
    return cosine_similarity(embeddings)

 Test
docs = [
    "Machine learning is awesome",
    "AI and ML are related fields",
    "The weather is nice today",
    "I love learning about AI",
]

 Pairwise similarities
similarity_matrix = document_similarity_matrix(docs, simple_embedding)
print("Similarity Matrix:")
print(similarity_matrix.round(3))

 Find most similar pair
max_sim = -1
best_pair = (None, None)
n = len(docs)
for i in range(n):
    for j in range(i+1, n):
        if similarity_matrix[i, j] > max_sim:
            max_sim = similarity_matrix[i, j]
            best_pair = (i, j)

print(f"\nMost similar pair:")
print(f"  '{docs[best_pair[0]]}'")
print(f"  '{docs[best_pair[1]]}'")
print(f"  Similarity: {max_sim:.3f}")
```

---

 Task 4: Text Recommendation System
Build a recommendation system based on text similarity.

```python
class TextRecommender:
    """
    Text recommendation system.
    """
    def __init__(self, embedding_model):
        """
        Initialize recommender.
        
        Args:
            embedding_model: Embedding function
        """
        self.embedding_model = embedding_model
        self.items = []
        self.embeddings = None
    
    def add_items(self, items: list):
        """
        Add items to catalog.
        
        Args:
            items (list): List of item descriptions
        """
        self.items = items
        self.embeddings = np.array([self.embedding_model(item) for item in items])
    
    def recommend(self, user_history: list, top_k: int = 5) -> list:
        """
        Recommend items based on user history.
        
        Args:
            user_history (list): List of items user liked
            top_k (int): Number of recommendations
            
        Returns:
            list: Recommended items with scores
        """
        if len(user_history) == 0:
             Return random items if no history
            return [(item, 0.0) for item in self.items[:top_k]]
        
         Get user profile (average of liked items)
        history_embeddings = np.array([self.embedding_model(item) for item in user_history])
        user_profile = history_embeddings.mean(axis=0)
        
         Calculate similarities
        similarities = cosine_similarity([user_profile], self.embeddings)[0]
        
         Remove already liked items
        for item in user_history:
            if item in self.items:
                idx = self.items.index(item)
                similarities[idx] = -1
        
         Get top K
        top_indices = similarities.argsort()[-top_k:][::-1]
        
        recommendations = [(self.items[i], similarities[i]) for i in top_indices if similarities[i] > 0]
        
        return recommendations

 Test - Movie recommendation example
recommender = TextRecommender(simple_embedding)

movies = [
    "A sci-fi adventure in space with aliens",
    "Romantic comedy about finding love in the city",
    "Action thriller with spies and explosions",
    "Horror movie with ghosts and haunted houses",
    "Documentary about nature and wildlife",
    "Animated film about talking animals",
]

recommender.add_items(movies)

user_likes = [
    "A sci-fi adventure in space with aliens",
    "Action thriller with spies and explosions",
]

recommendations = recommender.recommend(user_likes, top_k=3)
print("User liked:", user_likes)
print("\nRecommendations:")
for movie, score in recommendations:
    print(f"  {movie} (score: {score:.3f})")
```

---

 Task 5: Semantic Document Analyzer
Complete semantic analysis pipeline.

```python
class SemanticDocumentAnalyzer:
    """
    Complete semantic document analyzer.
    """
    def __init__(self, embedding_model):
        """
        Initialize analyzer.
        
        Args:
            embedding_model: Embedding function
        """
        self.embedding_model = embedding_model
    
    def analyze(self, document: str) -> dict:
        """
        Perform complete semantic analysis.
        
        Args:
            document (str): Input document
            
        Returns:
            dict: Analysis results
        """
         Get embedding
        embedding = self.embedding_model(document)
        
         Calculate basic statistics
        words = document.split()
        sentences = document.split('.')
        
        results = {
            'document': document,
            'embedding': embedding,
            'embedding_dim': len(embedding),
            'word_count': len(words),
            'sentence_count': len([s for s in sentences if s.strip()]),
            'avg_word_length': np.mean([len(w) for w in words]) if words else 0,
        }
        
        return results
    
    def compare_documents(self, doc1: str, doc2: str) -> dict:
        """
        Compare two documents semantically.
        
        Args:
            doc1 (str): First document
            doc2 (str): Second document
            
        Returns:
            dict: Comparison results
        """
         Get embeddings
        emb1 = self.embedding_model(doc1)
        emb2 = self.embedding_model(doc2)
        
         Calculate similarity
        similarity = cosine_similarity([emb1], [emb2])[0][0]
        
         Analyze each document
        analysis1 = self.analyze(doc1)
        analysis2 = self.analyze(doc2)
        
        return {
            'similarity': similarity,
            'doc1_stats': {
                'word_count': analysis1['word_count'],
                'sentence_count': analysis1['sentence_count']
            },
            'doc2_stats': {
                'word_count': analysis2['word_count'],
                'sentence_count': analysis2['sentence_count']
            }
        }

 Test
analyzer = SemanticDocumentAnalyzer(simple_embedding)

doc1 = "Natural language processing enables machines to understand human language"
doc2 = "NLP helps computers comprehend and process text from humans"
doc3 = "The stock market showed significant volatility today"

analysis = analyzer.analyze(doc1)
print("Document Analysis:")
print(f"  Word count: {analysis['word_count']}")
print(f"  Sentence count: {analysis['sentence_count']}")
print(f"  Embedding dimension: {analysis['embedding_dim']}")

comparison = analyzer.compare_documents(doc1, doc2)
print(f"\nSimilarity (doc1, doc2): {comparison['similarity']:.3f}")

comparison = analyzer.compare_documents(doc1, doc3)
print(f"Similarity (doc1, doc3): {comparison['similarity']:.3f}")
```

---

 ✅ Completion Checklist

- [ ] Semantic search engine implemented
- [ ] Document clustering working
- [ ] Document similarity calculator created
- [ ] Text recommendation system built
- [ ] Semantic document analyzer complete
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete semantic search and recommendation system:

```python
class SemanticSearchEngine:
    """
    Complete semantic search and recommendation engine.
    """
    def __init__(self, embedding_model):
        """
        Initialize engine.
        
        Args:
            embedding_model: Embedding function
        """
         Your implementation
        pass
    
    def index(self, documents: list, metadata: list = None):
        """
        Index documents with optional metadata.
        
        Args:
            documents (list): List of documents
            metadata (list): Optional metadata for each document
        """
         Your implementation
        pass
    
    def search(self, query: str, filters: dict = None, top_k: int = 10):
        """
        Search with optional filters.
        
        Args:
            query (str): Search query
            filters (dict): Metadata filters
            top_k (int): Number of results
            
        Returns:
            list: Search results
        """
         Your implementation
        pass
    
    def recommend(self, item_id: str, top_k: int = 5):
        """
        Recommend similar items.
        
        Args:
            item_id (str): Item ID
            top_k (int): Number of recommendations
            
        Returns:
            list: Recommendations
        """
         Your implementation
        pass
    
    def cluster(self, n_clusters: int = 5):
        """
        Cluster indexed documents.
        
        Args:
            n_clusters (int): Number of clusters
            
        Returns:
            dict: Cluster assignments
        """
         Your implementation
        pass
    
    def save_index(self, path: str):
        """Save index to disk."""
         Your implementation
        pass
    
    def load_index(self, path: str):
        """Load index from disk."""
         Your implementation
        pass
```
