 Exercise 3: Domain Adaptation

 🎯 Objective
Implement domain adaptation techniques for NLP tasks.

---

 📋 Tasks

 Task 1: Domain Shift Detection
Measure domain difference between datasets.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

class DomainShiftDetector:
    """
    Detect and measure domain shift between datasets.
    """
    def __init__(self):
        """Initialize detector."""
        self.vectorizer = TfidfVectorizer(max_features=5000)
    
    def compute_domain_distance(self, source_texts, target_texts):
        """
        Compute distance between source and target domains.
        
        Args:
            source_texts (list): Source domain texts
            target_texts (list): Target domain texts
            
        Returns:
            dict: Distance metrics
        """
         Vectorize
        all_texts = source_texts + target_texts
        vectors = self.vectorizer.fit_transform(all_texts)
        
        source_vectors = vectors[:len(source_texts)]
        target_vectors = vectors[len(source_texts):]
        
         Compute centroids
        source_centroid = source_vectors.mean(axis=0)
        target_centroid = target_vectors.mean(axis=0)
        
         Cosine distance
        cosine_dist = 1 - cosine_similarity(source_centroid, target_centroid)[0][0]
        
         Euclidean distance
        euclidean_dist = np.linalg.norm(source_centroid - target_centroid)
        
         Vocabulary overlap
        source_vocab = set()
        target_vocab = set()
        
        for text in source_texts:
            source_vocab.update(text.lower().split())
        
        for text in target_texts:
            target_vocab.update(text.lower().split())
        
        vocab_overlap = len(source_vocab & target_vocab) / len(source_vocab | target_vocab)
        
        return {
            'cosine_distance': cosine_dist,
            'euclidean_distance': euclidean_dist,
            'vocabulary_overlap': vocab_overlap,
            'source_vocab_size': len(source_vocab),
            'target_vocab_size': len(target_vocab)
        }
    
    def detect_domain_keywords(self, source_texts, target_texts, top_n=10):
        """
        Find domain-specific keywords.
        
        Args:
            source_texts (list): Source texts
            target_texts (list): Target texts
            top_n (int): Number of keywords
            
        Returns:
            dict: Domain-specific keywords
        """
        from collections import Counter
        
         Count words in each domain
        source_words = []
        for text in source_texts:
            source_words.extend(text.lower().split())
        
        target_words = []
        for text in target_texts:
            target_words.extend(text.lower().split())
        
        source_counter = Counter(source_words)
        target_counter = Counter(target_words)
        
         Find domain-specific words
        source_specific = []
        target_specific = []
        
        for word, count in source_counter.most_common(100):
            if word not in target_counter or source_counter[word] > target_counter[word]  10:
                source_specific.append((word, count))
        
        for word, count in target_counter.most_common(100):
            if word not in source_counter or target_counter[word] > source_counter[word]  10:
                target_specific.append((word, count))
        
        return {
            'source_specific': source_specific[:top_n],
            'target_specific': target_specific[:top_n]
        }

 Test
detector = DomainShiftDetector()

source_texts = [
    "Great product, excellent quality",
    "Fast shipping, good service",
    "Amazing purchase, highly recommend",
    "Perfect item, worth the price",
]

target_texts = [
    "Movie was boring and too long",
    "Excellent film, great acting",
    "Terrible plot, waste of time",
    "Best movie I've seen this year",
]

shift = detector.compute_domain_distance(source_texts, target_texts)
print("Domain Shift Detection:")
print(f"Cosine distance: {shift['cosine_distance']:.3f}")
print(f"Vocabulary overlap: {shift['vocabulary_overlap']:.3f}")

keywords = detector.detect_domain_keywords(source_texts, target_texts)
print("\nSource-specific keywords:", [w for w, _ in keywords['source_specific']])
print("Target-specific keywords:", [w for w, _ in keywords['target_specific']])
```

---

 Task 2: Fine-tuning Adaptation
Implement fine-tuning based domain adaptation.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

class FineTuningAdaptation:
    """
    Domain adaptation through fine-tuning.
    """
    def __init__(self, base_model=None):
        """
        Initialize adaptation.
        
        Args:
            base_model: Base model (default: LogisticRegression)
        """
        self.base_model = base_model or LogisticRegression(max_iter=1000)
        self.vectorizer = TfidfVectorizer(ngram_range=(1, 2))
        self.model = None
        self.is_adapted = False
    
    def pretrain(self, source_texts, source_labels):
        """
        Pre-train on source domain.
        
        Args:
            source_texts (list): Source texts
            source_labels (list): Source labels
        """
         Vectorize
        X_source = self.vectorizer.fit_transform(source_texts)
        
         Train
        self.model = LogisticRegression(max_iter=1000)
        self.model.fit(X_source, source_labels)
        
        print(f"Pre-trained on source: {len(source_texts)} samples")
    
    def adapt(self, target_texts, target_labels, epochs=5):
        """
        Adapt to target domain through continued training.
        
        Args:
            target_texts (list): Target texts
            target_labels (list): Target labels
            epochs (int): Training epochs
        """
        if self.model is None:
            raise ValueError("Must pretrain on source first")
        
         Transform target data
        X_target = self.vectorizer.transform(target_texts)
        
         Warm start (continue training)
        for epoch in range(epochs):
            self.model.fit(X_target, target_labels)
        
        self.is_adapted = True
        print(f"Adapted on target: {len(target_texts)} samples")
    
    def evaluate(self, texts, labels):
        """
        Evaluate model.
        
        Args:
            texts (list): Test texts
            labels (list): Test labels
            
        Returns:
            float: Accuracy
        """
        X = self.vectorizer.transform(texts)
        y_pred = self.model.predict(X)
        return accuracy_score(labels, y_pred)

 Test
adapter = FineTuningAdaptation()

source_texts = ["good", "bad"]  100
source_labels = [1, 0]  100

target_texts = ["excellent", "terrible"]  20
target_labels = [1, 0]  20

test_texts = ["good", "bad", "excellent", "terrible"]  10
test_labels = [1, 0, 1, 0]  10

 Pre-train
adapter.pretrain(source_texts, source_labels)
acc_before = adapter.evaluate(test_texts, test_labels)
print(f"\nAccuracy before adaptation: {acc_before:.3f}")

 Adapt
adapter.adapt(target_texts, target_labels, epochs=3)
acc_after = adapter.evaluate(test_texts, test_labels)
print(f"Accuracy after adaptation: {acc_after:.3f}")
print(f"Improvement: {acc_after - acc_before:.3f}")
```

---

 Task 3: Feature-Based Adaptation
Implement feature selection for domain adaptation.

```python
class FeatureBasedAdaptation:
    """
    Domain adaptation through feature selection.
    """
    def __init__(self):
        """Initialize adaptation."""
        self.vectorizer = None
        self.model = None
        self.domain_invariant_features = None
    
    def find_invariant_features(self, source_texts, target_texts, min_df=2):
        """
        Find domain-invariant features.
        
        Args:
            source_texts (list): Source texts
            target_texts (list): Target texts
            min_df (int): Minimum document frequency
            
        Returns:
            list: Domain-invariant feature indices
        """
         Create separate vectorizers
        source_vectorizer = TfidfVectorizer(min_df=min_df, max_features=5000)
        target_vectorizer = TfidfVectorizer(min_df=min_df, max_features=5000)
        
        source_vectorizer.fit(source_texts)
        target_vectorizer.fit(target_texts)
        
         Find common features
        source_vocab = set(source_vectorizer.vocabulary_.keys())
        target_vocab = set(target_vectorizer.vocabulary_.keys())
        
        common_vocab = source_vocab & target_vocab
        
        return list(common_vocab)
    
    def adapt(self, source_texts, source_labels, target_texts, target_labels):
        """
        Adapt using domain-invariant features.
        
        Args:
            source_texts (list): Source texts
            source_labels (list): Source labels
            target_texts (list): Target texts
            target_labels (list): Target labels
        """
         Find invariant features
        invariant_features = self.find_invariant_features(source_texts, target_texts)
        self.domain_invariant_features = invariant_features
        
         Create vectorizer with only invariant features
        self.vectorizer = TfidfVectorizer(vocabulary={w: i for i, w in enumerate(invariant_features)})
        
         Combine source and target
        all_texts = source_texts + target_texts
        all_labels = source_labels + target_labels
        
         Vectorize and train
        X = self.vectorizer.fit_transform(all_texts)
        self.model = LogisticRegression(max_iter=1000)
        self.model.fit(X, all_labels)
        
        print(f"Adapted using {len(invariant_features)} domain-invariant features")
    
    def predict(self, texts):
        """
        Make predictions.
        
        Args:
            texts (list): Input texts
            
        Returns:
            list: Predictions
        """
        X = self.vectorizer.transform(texts)
        return self.model.predict(X)

 Test
feature_adapter = FeatureBasedAdaptation()
feature_adapter.adapt(source_texts, source_labels, target_texts, target_labels)
acc_feature = feature_adapter.predict(test_texts)
print(f"\nFeature-based accuracy: {(acc_feature == test_labels).mean():.3f}")
```

---

 Task 4: Data Augmentation for Adaptation
Augment target domain data.

```python
import random

class DataAugmenter:
    """
    Augment data for better domain adaptation.
    """
    def __init__(self):
        """Initialize augmenter."""
        self.synonyms = {
            'good': ['great', 'excellent', 'nice', 'positive'],
            'bad': ['terrible', 'awful', 'poor', 'negative'],
            'happy': ['joyful', 'pleased', 'delighted'],
            'sad': ['unhappy', 'sorrowful', 'dejected']
        }
    
    def synonym_replacement(self, text, n=1):
        """
        Replace words with synonyms.
        
        Args:
            text (str): Input text
            n (int): Number of replacements
            
        Returns:
            str: Augmented text
        """
        words = text.split()
        new_words = words.copy()
        
         Find replaceable words
        replaceable = [i for i, word in enumerate(words) if word.lower() in self.synonyms]
        
        if not replaceable:
            return text
        
         Replace n words
        random.shuffle(replaceable)
        for i in replaceable[:n]:
            word = words[i].lower()
            if word in self.synonyms:
                synonyms = self.synonyms[word]
                new_words[i] = random.choice(synonyms)
        
        return ' '.join(new_words)
    
    def random_deletion(self, text, p=0.1):
        """
        Randomly delete words.
        
        Args:
            text (str): Input text
            p (float): Deletion probability
            
        Returns:
            str: Augmented text
        """
        words = text.split()
        
        if len(words) == 1:
            return text
        
        new_words = []
        for word in words:
            if random.random() > p:
                new_words.append(word)
        
        if not new_words:
            return random.choice(words)
        
        return ' '.join(new_words)
    
    def augment_dataset(self, texts, labels, multiplier=2):
        """
        Augment entire dataset.
        
        Args:
            texts (list): Input texts
            labels (list): Input labels
            multiplier (int): Augmentation factor
            
        Returns:
            tuple: (augmented_texts, augmented_labels)
        """
        augmented_texts = list(texts)
        augmented_labels = list(labels)
        
        for _ in range(multiplier - 1):
            for text, label in zip(texts, labels):
                 Random augmentation
                if random.random() < 0.5:
                    aug_text = self.synonym_replacement(text)
                else:
                    aug_text = self.random_deletion(text)
                
                augmented_texts.append(aug_text)
                augmented_labels.append(label)
        
        return augmented_texts, augmented_labels

 Test
augmenter = DataAugmenter()
test_text = "This product is good"
print("Original:", test_text)
print("Synonym replacement:", augmenter.synonym_replacement(test_text))
print("Random deletion:", augmenter.random_deletion(test_text, p=0.2))

 Augment dataset
aug_texts, aug_labels = augmenter.augment_dataset(target_texts, target_labels, multiplier=3)
print(f"\nOriginal dataset: {len(target_texts)} samples")
print(f"Augmented dataset: {len(aug_texts)} samples")
```

---

 Task 5: Complete Adaptation Pipeline
Create end-to-end adaptation system.

```python
class DomainAdaptationPipeline:
    """
    Complete domain adaptation pipeline.
    """
    def __init__(self, method='fine_tuning'):
        """
        Initialize pipeline.
        
        Args:
            method (str): Adaptation method
        """
        self.method = method
        self.model = None
        self.vectorizer = None
        self.augmenter = DataAugmenter()
        self.detector = DomainShiftDetector()
    
    def analyze_domain_shift(self, source_texts, target_texts):
        """
        Analyze domain shift.
        
        Args:
            source_texts (list): Source texts
            target_texts (list): Target texts
            
        Returns:
            dict: Analysis results
        """
        return self.detector.compute_domain_distance(source_texts, target_texts)
    
    def adapt(self, source_texts, source_labels, target_texts, target_labels, 
              use_augmentation=True):
        """
        Adapt model to target domain.
        
        Args:
            source_texts (list): Source texts
            source_labels (list): Source labels
            target_texts (list): Target texts
            target_labels (list): Target labels
            use_augmentation (bool): Whether to augment data
        """
         Augment if requested
        if use_augmentation:
            target_texts, target_labels = self.augmenter.augment_dataset(
                target_texts, target_labels, multiplier=2
            )
        
         Adapt based on method
        if self.method == 'fine_tuning':
            adapter = FineTuningAdaptation()
            adapter.pretrain(source_texts, source_labels)
            adapter.adapt(target_texts, target_labels)
            self.model = adapter.model
            self.vectorizer = adapter.vectorizer
        elif self.method == 'feature_based':
            adapter = FeatureBasedAdaptation()
            adapter.adapt(source_texts, source_labels, target_texts, target_labels)
            self.model = adapter.model
            self.vectorizer = adapter.vectorizer
        else:
            raise ValueError(f"Unknown method: {self.method}")
        
        print(f"Adaptation complete using {self.method}")
    
    def evaluate(self, test_texts, test_labels):
        """
        Evaluate adapted model.
        
        Args:
            test_texts (list): Test texts
            test_labels (list): Test labels
            
        Returns:
            dict: Evaluation metrics
        """
        X_test = self.vectorizer.transform(test_texts)
        y_pred = self.model.predict(X_test)
        
        return {
            'accuracy': accuracy_score(test_labels, y_pred),
            'predictions': y_pred
        }

 Test
pipeline = DomainAdaptationPipeline(method='fine_tuning')

 Analyze shift
shift_analysis = pipeline.analyze_domain_shift(source_texts, target_texts)
print("Domain Shift Analysis:", shift_analysis)

 Adapt
pipeline.adapt(source_texts, source_labels, target_texts, target_labels, use_augmentation=True)

 Evaluate
results = pipeline.evaluate(test_texts, test_labels)
print(f"\nFinal accuracy: {results['accuracy']:.3f}")
```

---

 ✅ Completion Checklist

- [ ] Domain shift detection working
- [ ] Fine-tuning adaptation implemented
- [ ] Feature-based adaptation created
- [ ] Data augmentation working
- [ ] Complete pipeline functional
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create complete domain adaptation system:

```python
class MultiDomainNLPSystem:
    """
    NLP system supporting multiple domains.
    """
    def __init__(self):
        """Initialize multi-domain system."""
        self.domain_models = {}
        self.adaptation_pipeline = DomainAdaptationPipeline()
    
    def add_domain(self, domain_name, texts, labels):
        """
        Add domain-specific model.
        
        Args:
            domain_name (str): Domain name
            texts (list): Domain texts
            labels (list): Domain labels
        """
         Your implementation
        pass
    
    def predict(self, text, domain=None):
        """
        Predict with optional domain specification.
        
        Args:
            text (str): Input text
            domain (str): Domain name (optional)
            
        Returns:
            prediction
        """
         Your implementation
        pass
    
    def adapt_to_new_domain(self, new_domain_texts, new_domain_labels, source_domain):
        """
        Adapt existing model to new domain.
        
        Args:
            new_domain_texts (list): New domain texts
            new_domain_labels (list): New domain labels
            source_domain (str): Source domain name
        """
         Your implementation
        pass
    
    def compare_domains(self, domain1, domain2):
        """
        Compare two domains.
        
        Args:
            domain1 (str): First domain
            domain2 (str): Second domain
            
        Returns:
            dict: Comparison metrics
        """
         Your implementation
        pass
```
