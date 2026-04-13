 Exercise 1: Word Embeddings Implementation

 🎯 Objective
Implement and train word embedding models from scratch and compare with pre-trained embeddings.

---

 📋 Tasks

 Task 1: Word2Vec from Scratch
Implement a simplified Word2Vec (Skip-gram) model.

```python
import numpy as np
from collections import Counter, defaultdict

class SkipGram:
    """
    Simplified Skip-gram implementation.
    """
    def __init__(self, vocab_size, embedding_dim=50, window_size=2):
        """
        Initialize Skip-gram model.
        
        Args:
            vocab_size (int): Size of vocabulary
            embedding_dim (int): Dimension of embeddings
            window_size (int): Context window size
        """
        self.vocab_size = vocab_size
        self.embedding_dim = embedding_dim
        self.window_size = window_size
        
         Initialize embeddings randomly
        self.center_embeddings = np.random.randn(vocab_size, embedding_dim)  0.01
        self.context_embeddings = np.random.randn(vocab_size, embedding_dim)  0.01
        
         Vocabulary
        self.word2idx = {}
        self.idx2word = {}
    
    def build_vocab(self, sentences):
        """
        Build vocabulary from sentences.
        
        Args:
            sentences (list): List of tokenized sentences
        """
        word_counts = Counter(word for sent in sentences for word in sent)
        words = [word for word, count in word_counts.most_common(self.vocab_size-2)]
        
         Add special tokens
        words = ['<PAD>', '<UNK>'] + words
        
        self.word2idx = {word: idx for idx, word in enumerate(words)}
        self.idx2word = {idx: word for word, idx in self.word2idx.items()}
        self.vocab_size = len(words)
    
    def generate_training_pairs(self, sentences):
        """
        Generate (center, context) pairs for training.
        
        Args:
            sentences (list): List of tokenized sentences
            
        Returns:
            list: List of (center, context) pairs
        """
        pairs = []
        
        for sent in sentences:
             Convert words to indices
            indices = [self.word2idx.get(word, 1) for word in sent]   1 = <UNK>
            
             Generate pairs
            for i, center in enumerate(indices):
                 Get context words
                start = max(0, i - self.window_size)
                end = min(len(indices), i + self.window_size + 1)
                
                for j in range(start, end):
                    if i != j:
                        context = indices[j]
                        pairs.append((center, context))
        
        return pairs
    
    def train(self, sentences, epochs=10, learning_rate=0.01):
        """
        Train the model.
        
        Args:
            sentences (list): List of tokenized sentences
            epochs (int): Number of training epochs
            learning_rate (float): Learning rate
        """
         Build vocabulary
        self.build_vocab(sentences)
        
         Generate training pairs
        pairs = self.generate_training_pairs(sentences)
        
         Training loop (simplified - no negative sampling)
        for epoch in range(epochs):
            loss = 0
            
            for center, context in pairs:
                 Forward pass
                center_emb = self.center_embeddings[center]
                context_emb = self.context_embeddings[context]
                
                 Dot product
                score = np.dot(center_emb, context_emb)
                
                 Sigmoid
                prob = 1 / (1 + np.exp(-score))
                
                 Loss (binary cross-entropy)
                loss -= np.log(prob + 1e-10)
                
                 Backward pass (gradient)
                grad = prob - 1
                
                 Update embeddings
                self.center_embeddings[center] -= learning_rate  grad  context_emb
                self.context_embeddings[context] -= learning_rate  grad  center_emb
            
            if epoch % 2 == 0:
                print(f"Epoch {epoch}, Loss: {loss:.4f}")
    
    def get_embedding(self, word):
        """
        Get embedding for a word.
        
        Args:
            word (str): Input word
            
        Returns:
            array: Embedding vector
        """
        idx = self.word2idx.get(word, 1)
        return self.center_embeddings[idx]
    
    def similarity(self, word1, word2):
        """
        Calculate cosine similarity between two words.
        
        Args:
            word1 (str): First word
            word2 (str): Second word
            
        Returns:
            float: Cosine similarity
        """
        emb1 = self.get_embedding(word1)
        emb2 = self.get_embedding(word2)
        
        return np.dot(emb1, emb2) / (np.linalg.norm(emb1)  np.linalg.norm(emb2))

 Test
sentences = [
    ['natural', 'language', 'processing'],
    ['machine', 'learning', 'language'],
    ['deep', 'learning', 'nlp'],
    ['word', 'embeddings', 'semantic'],
    ['neural', 'networks', 'data'],
]  100   Repeat for more training data

model = SkipGram(vocab_size=100, embedding_dim=50)
model.train(sentences, epochs=10, learning_rate=0.01)

print("\nEmbeddings:")
print("'language':", model.get_embedding('language')[:5])
print("'learning':", model.get_embedding('learning')[:5])

print("\nSimilarity:")
print(f"language-learning: {model.similarity('language', 'learning'):.4f}")
print(f"language-natural: {model.similarity('language', 'natural'):.4f}")
```

---

 Task 2: GloVe-style Co-occurrence Matrix
Build a co-occurrence matrix for GloVe-style training.

```python
import numpy as np
from collections import defaultdict

class CooccurrenceMatrix:
    """
    Build co-occurrence matrix for GloVe-style training.
    """
    def __init__(self, window_size=5, min_count=5):
        """
        Initialize co-occurrence matrix builder.
        
        Args:
            window_size (int): Context window size
            min_count (int): Minimum word frequency
        """
        self.window_size = window_size
        self.min_count = min_count
        self.word2idx = {}
        self.idx2word = {}
        self.cooccurrence = defaultdict(float)
    
    def build(self, sentences):
        """
        Build co-occurrence matrix from sentences.
        
        Args:
            sentences (list): List of tokenized sentences
        """
         Count word frequencies
        word_counts = defaultdict(int)
        for sent in sentences:
            for word in sent:
                word_counts[word] += 1
        
         Build vocabulary
        words = [word for word, count in word_counts.items() if count >= self.min_count]
        self.word2idx = {word: idx for idx, word in enumerate(words)}
        self.idx2word = {idx: word for word, idx in self.word2idx.items()}
        
         Build co-occurrence matrix
        for sent in sentences:
            for i, word in enumerate(sent):
                if word not in self.word2idx:
                    continue
                
                center_idx = self.word2idx[word]
                
                 Get context words
                start = max(0, i - self.window_size)
                end = min(len(sent), i + self.window_size + 1)
                
                for j in range(start, end):
                    if i == j:
                        continue
                    
                    context_word = sent[j]
                    if context_word not in self.word2idx:
                        continue
                    
                    context_idx = self.word2idx[context_word]
                    
                     Increment co-occurrence with distance weighting
                    distance = abs(j - i)
                    weight = 1.0 / distance
                    self.cooccurrence[(center_idx, context_idx)] += weight
    
    def get_cooccurrence(self, word1, word2):
        """
        Get co-occurrence count for two words.
        
        Args:
            word1 (str): First word
            word2 (str): Second word
            
        Returns:
            float: Co-occurrence count
        """
        if word1 not in self.word2idx or word2 not in self.word2idx:
            return 0.0
        
        idx1 = self.word2idx[word1]
        idx2 = self.word2idx[word2]
        
        return self.cooccurrence[(idx1, idx2)]
    
    def get_top_context_words(self, word, top_n=5):
        """
        Get top context words for a given word.
        
        Args:
            word (str): Input word
            top_n (int): Number of top words
            
        Returns:
            list: List of (context_word, count) tuples
        """
        if word not in self.word2idx:
            return []
        
        center_idx = self.word2idx[word]
        
         Get all co-occurrences for this word
        context_counts = []
        for (idx1, idx2), count in self.cooccurrence.items():
            if idx1 == center_idx:
                context_word = self.idx2word[idx2]
                context_counts.append((context_word, count))
        
         Sort and return top N
        context_counts.sort(key=lambda x: x[1], reverse=True)
        return context_counts[:top_n]

 Test
sentences = [
    ['natural', 'language', 'processing', 'is', 'fascinating'],
    ['machine', 'learning', 'enables', 'language', 'understanding'],
    ['deep', 'learning', 'improves', 'nlp', 'applications'],
    ['word', 'embeddings', 'capture', 'semantic', 'meaning'],
    ['neural', 'networks', 'learn', 'from', 'data'],
]  50

cooc = CooccurrenceMatrix(window_size=3, min_count=2)
cooc.build(sentences)

print("Vocabulary size:", len(cooc.word2idx))

print("\nCo-occurrence counts:")
print(f"language-learning: {cooc.get_cooccurrence('language', 'learning')}")
print(f"language-natural: {cooc.get_cooccurrence('language', 'natural')}")
print(f"learning-machine: {cooc.get_cooccurrence('learning', 'machine')}")

print("\nTop context words for 'learning':")
for word, count in cooc.get_top_context_words('learning', top_n=5):
    print(f"  {word}: {count:.2f}")
```

---

 Task 3: Word Analogies
Implement word analogy solver using embeddings.

```python
import numpy as np

def cosine_similarity(v1, v2):
    """Calculate cosine similarity between two vectors."""
    return np.dot(v1, v2) / (np.linalg.norm(v1)  np.linalg.norm(v2))

def solve_analogy(embeddings, word_a, word_b, word_c, top_n=5):
    """
    Solve word analogy: word_a - word_b + word_c = ?
    
    Args:
        embeddings (dict): Dictionary of word -> embedding
        word_a (str): First word
        word_b (str): Second word
        word_c (str): Third word
        top_n (int): Number of top results
        
    Returns:
        list: List of (word, similarity) tuples
    """
    if word_a not in embeddings or word_b not in embeddings or word_c not in embeddings:
        return []
    
     Calculate target vector
    target = embeddings[word_a] - embeddings[word_b] + embeddings[word_c]
    
     Find closest words
    similarities = []
    for word, emb in embeddings.items():
        if word in [word_a, word_b, word_c]:
            continue
        
        sim = cosine_similarity(target, emb)
        similarities.append((word, sim))
    
     Sort and return top N
    similarities.sort(key=lambda x: x[1], reverse=True)
    return similarities[:top_n]

 Create simple embeddings for testing
embeddings = {
    'king': np.array([0.5, 0.3, 0.2]),
    'queen': np.array([0.5, 0.4, 0.3]),
    'man': np.array([0.3, 0.2, 0.1]),
    'woman': np.array([0.3, 0.3, 0.2]),
    'prince': np.array([0.4, 0.25, 0.15]),
    'princess': np.array([0.4, 0.35, 0.25]),
}

 Test analogies
print("Word Analogies:")

 king - man + woman = ?
result = solve_analogy(embeddings, 'king', 'man', 'woman')
print(f"king - man + woman = {result[0][0]} (similarity: {result[0][1]:.3f})")

 queen - woman + man = ?
result = solve_analogy(embeddings, 'queen', 'woman', 'man')
print(f"queen - woman + man = {result[0][0]} (similarity: {result[0][1]:.3f})")

 prince - king + queen = ?
result = solve_analogy(embeddings, 'prince', 'king', 'queen')
print(f"prince - king + queen = {result[0][0]} (similarity: {result[0][1]:.3f})")
```

---

 Task 4: Pre-trained Embeddings Comparison
Load and compare pre-trained embeddings.

```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

def load_glove_embeddings(glove_file, num_words=None):
    """
    Load GloVe embeddings from file.
    
    Args:
        glove_file (str): Path to GloVe file
        num_words (int): Number of words to load (None for all)
        
    Returns:
        dict: Dictionary of word -> embedding
    """
    embeddings = {}
    
    with open(glove_file, 'r', encoding='utf-8') as f:
        for i, line in enumerate(f):
            if num_words and i >= num_words:
                break
            
            values = line.split()
            word = values[0]
            vector = np.asarray(values[1:], dtype='float32')
            embeddings[word] = vector
    
    return embeddings

def visualize_embeddings(embeddings, words, method='pca'):
    """
    Visualize embeddings in 2D.
    
    Args:
        embeddings (dict): Word embeddings
        words (list): Words to visualize
        method (str): 'pca' or 'tsne'
    """
    from sklearn.decomposition import PCA
    from sklearn.manifold import TSNE
    import matplotlib.pyplot as plt
    
     Get vectors for specified words
    vectors = np.array([embeddings[w] for w in words if w in embeddings])
    valid_words = [w for w in words if w in embeddings]
    
    if method == 'pca':
        reducer = PCA(n_components=2)
    else:
        reducer = TSNE(n_components=2)
    
    vectors_2d = reducer.fit_transform(vectors)
    
     Plot
    plt.figure(figsize=(10, 8))
    plt.scatter(vectors_2d[:, 0], vectors_2d[:, 1])
    
    for i, word in enumerate(valid_words):
        plt.annotate(word, (vectors_2d[i, 0], vectors_2d[i, 1]))
    
    plt.title(f'Embedding Visualization ({method.upper()})')
    plt.xlabel('Dimension 1')
    plt.ylabel('Dimension 2')
    plt.show()

 Example usage (requires GloVe file)
 embeddings = load_glove_embeddings('glove.6B.50d.txt', num_words=10000)
 
 words_to_visualize = ['king', 'queen', 'man', 'woman', 'prince', 'princess',
                       'cat', 'dog', 'apple', 'orange', 'car', 'bus']
 visualize_embeddings(embeddings, words_to_visualize, method='pca')

print("Note: This task requires downloading GloVe embeddings from:")
print("https://nlp.stanford.edu/projects/glove/")
print("\nAfter downloading, uncomment the code above to run.")
```

---

 Task 5: Embedding Evaluation
Evaluate embeddings on word similarity task.

```python
def evaluate_embeddings(embeddings, similarity_pairs):
    """
    Evaluate embeddings on word similarity task.
    
    Args:
        embeddings (dict): Word embeddings
        similarity_pairs (list): List of (word1, word2, human_score) tuples
        
    Returns:
        dict: Evaluation metrics
    """
    from scipy.stats import spearmanr
    
    model_scores = []
    human_scores = []
    valid_pairs = []
    
    for word1, word2, human_score in similarity_pairs:
        if word1 in embeddings and word2 in embeddings:
            model_score = cosine_similarity(embeddings[word1], embeddings[word2])
            model_scores.append(model_score)
            human_scores.append(human_score)
            valid_pairs.append((word1, word2))
        else:
            print(f"Warning: Missing word in pair ({word1}, {word2})")
    
     Calculate correlation
    correlation, p_value = spearmanr(human_scores, model_scores)
    
    return {
        'correlation': correlation,
        'p_value': p_value,
        'num_pairs': len(valid_pairs)
    }

 Sample similarity pairs (use SimLex-999 or WordSim-353 for real evaluation)
similarity_pairs = [
    ('king', 'queen', 0.9),
    ('king', 'man', 0.7),
    ('king', 'car', 0.1),
    ('cat', 'dog', 0.8),
    ('cat', 'car', 0.2),
]

 Use embeddings from Task 1 or Task 4
 results = evaluate_embeddings(embeddings, similarity_pairs)
 print(f"Spearman correlation: {results['correlation']:.3f}")
 print(f"P-value: {results['p_value']:.3f}")
 print(f"Valid pairs: {results['num_pairs']}")

print("Note: For real evaluation, use datasets like:")
print("- WordSim-353: http://www.cs.technion.ac.il/~gabr/resources/data/wordsim353/")
print("- SimLex-999: https://fh295.github.io/simlex.html")
```

---

 ✅ Completion Checklist

- [ ] Skip-gram model implemented
- [ ] Co-occurrence matrix built
- [ ] Word analogy solver working
- [ ] Pre-trained embeddings loaded
- [ ] Embedding evaluation completed
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Implement complete embedding training and evaluation pipeline:

```python
class EmbeddingTrainer:
    """
    Complete embedding training pipeline.
    """
    def __init__(self, method='word2vec', embedding_dim=100):
        """
        Initialize trainer.
        
        Args:
            method (str): 'word2vec', 'glove', or 'fasttext'
            embedding_dim (int): Embedding dimension
        """
         Your implementation
        pass
    
    def train(self, corpus, kwargs):
        """
        Train embeddings.
        
        Args:
            corpus (list): List of tokenized sentences
            kwargs: Additional training parameters
            
        Returns:
            trained embeddings
        """
         Your implementation
        pass
    
    def evaluate(self, similarity_dataset):
        """
        Evaluate on similarity task.
        
        Args:
            similarity_dataset (list): Similarity pairs
            
        Returns:
            dict: Evaluation metrics
        """
         Your implementation
        pass
    
    def save(self, path):
        """Save embeddings."""
         Your implementation
        pass
    
    def load(self, path):
        """Load embeddings."""
         Your implementation
        pass
```
