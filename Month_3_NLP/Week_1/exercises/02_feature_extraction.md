 Exercise 2: Feature Extraction & Vectorization

 🎯 Objective
Master different text vectorization techniques and understand their trade-offs for downstream ML tasks.

---

 📋 Tasks

 Task 1: Bag of Words Implementation
Implement Bag of Words from scratch and compare with scikit-learn's CountVectorizer.

```python
import numpy as np
from collections import Counter
from sklearn.feature_extraction.text import CountVectorizer

def bag_of_words(corpus):
    """
    Implement Bag of Words from scratch.
    
    Args:
        corpus (list): List of documents (strings)
        
    Returns:
        tuple: (vocabulary list, document-term matrix)
    """
     Your implementation here
     1. Tokenize all documents
     2. Build vocabulary
     3. Create document-term matrix
    pass

 Test corpus
corpus = [
    "The cat sat on the mat",
    "The dog sat on the log",
    "The cat chased the dog"
]

 Your implementation
vocab, dtm_custom = bag_of_words(corpus)

 Scikit-learn implementation
vectorizer = CountVectorizer()
dtm_sklearn = vectorizer.fit_transform(corpus)

print("Your Vocabulary:", vocab)
print("sklearn Vocabulary:", vectorizer.get_feature_names_out())
print("\nYour DTM:")
print(dtm_custom)
print("\nsklearn DTM:")
print(dtm_sklearn.toarray())
```

Analysis Questions:
1. How does your implementation handle word ordering?
2. What happens with repeated words in a document?
3. How would you handle case sensitivity?

---

 Task 2: TF-IDF from Scratch
Implement TF-IDF calculation manually.

```python
import math

def tfidf(corpus):
    """
    Implement TF-IDF from scratch.
    
    Args:
        corpus (list): List of documents
        
    Returns:
        tuple: (vocabulary, tfidf_matrix)
    """
     Your implementation here
     1. Calculate term frequency (TF)
     2. Calculate document frequency (DF)
     3. Calculate inverse document frequency (IDF)
     4. Calculate TF-IDF = TF  IDF
    pass

 Test
vocab, tfidf_custom = tfidf(corpus)

print("Custom TF-IDF:")
print(tfidf_custom)

 Compare with sklearn
from sklearn.feature_extraction.text import TfidfVectorizer
tfidf_vectorizer = TfidfVectorizer()
tfidf_sklearn = tfidf_vectorizer.fit_transform(corpus)

print("\nsklearn TF-IDF:")
print(tfidf_sklearn.toarray())
```

Analysis Questions:
1. Why do we use log in IDF calculation?
2. What is the effect of a high TF-IDF score?
3. How does TF-IDF handle words that appear in all documents?

---

 Task 3: N-gram Analysis
Implement and analyze different n-gram ranges.

```python
def extract_ngrams(text, n):
    """
    Extract n-grams from text.
    
    Args:
        text (str): Input text
        n (int): N-gram size
        
    Returns:
        list: List of n-grams
    """
     Your implementation here
    pass

 Test
text = "Natural language processing is fascinating"
print("Unigrams:", extract_ngrams(text, 1))
print("Bigrams:", extract_ngrams(text, 2))
print("Trigrams:", extract_ngrams(text, 3))

 Compare with sklearn
vectorizer_uni = CountVectorizer(ngram_range=(1, 1))
vectorizer_bi = CountVectorizer(ngram_range=(2, 2))
vectorizer_tri = CountVectorizer(ngram_range=(3, 3))

corpus = ["I love machine learning", "Machine learning is great", "I love deep learning"]

print("\nsklearn Unigrams:", vectorizer_uni.fit_transform(corpus).toarray())
print("Vocabulary:", vectorizer_uni.get_feature_names_out())

print("\nsklearn Bigrams:", vectorizer_bi.fit_transform(corpus).toarray())
print("Vocabulary:", vectorizer_bi.get_feature_names_out())
```

Analysis Questions:
1. What is the vocabulary size for each n-gram range?
2. Which n-gram range captures more context?
3. What is the trade-off between context and vocabulary size?

---

 Task 4: Feature Selection
Implement feature selection methods to reduce dimensionality.

```python
from sklearn.feature_selection import SelectKBest, chi2, f_classif
import numpy as np

def select_features(X, y, k=10, method='chi2'):
    """
    Select top k features using specified method.
    
    Args:
        X (array): Feature matrix
        y (array): Labels
        k (int): Number of features to select
        method (str): 'chi2' or 'f_classif'
        
    Returns:
        tuple: (selected features, feature names, scores)
    """
     Your implementation here
    pass

 Sample data (replace with real data)
X = np.random.rand(100, 50)   100 samples, 50 features
y = np.random.randint(0, 2, 100)   Binary labels

selected_features, feature_names, scores = select_features(X, y, k=10)

print("Top 10 Features:")
for i, (name, score) in enumerate(zip(feature_names, scores), 1):
    print(f"{i}. {name}: {score:.4f}")
```

---

 Task 5: Feature Comparison on Classification Task
Compare different feature extraction methods on a text classification task.

```python
from sklearn.model_selection import train_test_split
from sklearn.naive_bayes import MultinomialNB
from sklearn.metrics import accuracy_score, classification_report

def compare_features(corpus, labels):
    """
    Compare different feature extraction methods.
    
    Args:
        corpus (list): List of documents
        labels (list): List of labels
        
    Returns:
        dict: Performance metrics for each method
    """
    X_train, X_test, y_train, y_test = train_test_split(
        corpus, labels, test_size=0.2, random_state=42
    )
    
    methods = {
        'BoW': CountVectorizer(),
        'TF-IDF': TfidfVectorizer(),
        'BoW Bigrams': CountVectorizer(ngram_range=(1, 2)),
        'TF-IDF Bigrams': TfidfVectorizer(ngram_range=(1, 2)),
    }
    
    results = {}
    
    for name, vectorizer in methods.items():
        X_train_vec = vectorizer.fit_transform(X_train)
        X_test_vec = vectorizer.transform(X_test)
        
        model = MultinomialNB()
        model.fit(X_train_vec, y_train)
        y_pred = model.predict(X_test_vec)
        
        results[name] = {
            'accuracy': accuracy_score(y_test, y_pred),
            'n_features': X_train_vec.shape[1]
        }
    
    return results

 Test with sample data
corpus = ["This is positive" if i % 2 == 0 else "This is negative" for i in range(100)]
labels = [1 if i % 2 == 0 else 0 for i in range(100)]

results = compare_features(corpus, labels)
print("Feature Extraction Comparison:")
for method, metrics in results.items():
    print(f"{method}: Accuracy={metrics['accuracy']:.3f}, Features={metrics['n_features']}")
```

---

 ✅ Completion Checklist

- [ ] Bag of Words implemented from scratch
- [ ] TF-IDF implemented from scratch
- [ ] N-gram extraction working
- [ ] Feature selection implemented
- [ ] Comparison analysis completed
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a feature extraction pipeline that automatically selects the best method:

```python
class AutoFeatureExtractor:
    """
    Automatically select the best feature extraction method.
    """
    def __init__(self, max_features=10000, n_trials=5):
        """
        Initialize auto feature extractor.
        
        Args:
            max_features (int): Maximum number of features
            n_trials (int): Number of trials for each method
        """
         Your implementation
        pass
    
    def fit(self, X, y):
        """
        Find best feature extraction method.
        
        Args:
            X (list): Training documents
            y (list): Training labels
            
        Returns:
            self
        """
         Your implementation
        pass
    
    def transform(self, X):
        """
        Transform documents using best method.
        
        Args:
            X (list): Documents to transform
            
        Returns:
            array: Feature matrix
        """
         Your implementation
        pass
    
    def fit_transform(self, X, y):
        """
        Fit and transform.
        
        Args:
            X (list): Training documents
            y (list): Training labels
            
        Returns:
            array: Feature matrix
        """
        self.fit(X, y)
        return self.transform(X)
```
