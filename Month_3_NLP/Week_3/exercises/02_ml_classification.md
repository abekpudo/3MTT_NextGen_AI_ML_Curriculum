 Exercise 2: ML-Based Text Classification

 🎯 Objective
Build and evaluate traditional ML-based text classification systems.

---

 📋 Tasks

 Task 1: Feature Extraction Comparison
Compare different feature extraction methods.

```python
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
from sklearn.model_selection import train_test_split
from sklearn.naive_bayes import MultinomialNB
from sklearn.metrics import accuracy_score, classification_report
import pandas as pd

def compare_feature_extraction(texts: list, labels: list) -> pd.DataFrame:
    """
    Compare different feature extraction methods.
    
    Args:
        texts (list): List of texts
        labels (list): List of labels
        
    Returns:
        DataFrame: Comparison results
    """
    X_train, X_test, y_train, y_test = train_test_split(
        texts, labels, test_size=0.2, random_state=42, stratify=labels
    )
    
    methods = {
        'BoW': CountVectorizer(),
        'TF-IDF': TfidfVectorizer(),
        'BoW Bigrams': CountVectorizer(ngram_range=(1, 2)),
        'TF-IDF Bigrams': TfidfVectorizer(ngram_range=(1, 2)),
        'TF-IDF Trigrams': TfidfVectorizer(ngram_range=(1, 3)),
    }
    
    results = []
    
    for name, vectorizer in methods.items():
         Fit and transform
        X_train_vec = vectorizer.fit_transform(X_train)
        X_test_vec = vectorizer.transform(X_test)
        
         Train model
        model = MultinomialNB()
        model.fit(X_train_vec, y_train)
        
         Predict
        y_pred = model.predict(X_test_vec)
        
         Metrics
        accuracy = accuracy_score(y_test, y_pred)
        
        results.append({
            'method': name,
            'accuracy': accuracy,
            'vocab_size': len(vectorizer.vocabulary_),
            'train_shape': X_train_vec.shape,
            'test_shape': X_test_vec.shape
        })
    
    return pd.DataFrame(results)

 Test
texts = [
    "This product is amazing and I love it",
    "Terrible quality, would not recommend",
    "Great value for money",
    "Worst purchase ever made",
    "Good product overall",
    "Absolutely terrible experience",
]  20   Repeat for more data

labels = [1, 0, 1, 0, 1, 0]  20   1=Positive, 0=Negative

results = compare_feature_extraction(texts, labels)
print("Feature Extraction Comparison:")
print(results)
```

---

 Task 2: Model Comparison
Compare different ML classifiers.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.svm import LinearSVC
from sklearn.naive_bayes import MultinomialNB
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score
import numpy as np

def compare_classifiers(X_train, y_train, X_test, y_test, vectorizer) -> pd.DataFrame:
    """
    Compare different ML classifiers.
    
    Args:
        X_train: Training texts
        y_train: Training labels
        X_test: Test texts
        y_test: Test labels
        vectorizer: Feature vectorizer
        
    Returns:
        DataFrame: Comparison results
    """
     Vectorize
    X_train_vec = vectorizer.fit_transform(X_train)
    X_test_vec = vectorizer.transform(X_test)
    
     Classifiers
    classifiers = {
        'Naive Bayes': MultinomialNB(),
        'Logistic Regression': LogisticRegression(max_iter=1000),
        'Linear SVM': LinearSVC(max_iter=1000),
        'Random Forest': RandomForestClassifier(n_estimators=100, n_jobs=-1),
    }
    
    results = []
    
    for name, model in classifiers.items():
         Cross-validation
        cv_scores = cross_val_score(model, X_train_vec, y_train, cv=5)
        
         Train
        model.fit(X_train_vec, y_train)
        
         Test
        y_pred = model.predict(X_test_vec)
        accuracy = accuracy_score(y_test, y_pred)
        
        results.append({
            'classifier': name,
            'cv_accuracy_mean': cv_scores.mean(),
            'cv_accuracy_std': cv_scores.std(),
            'test_accuracy': accuracy
        })
    
    return pd.DataFrame(results)

 Test
X_train, X_test, y_train, y_test = train_test_split(
    texts, labels, test_size=0.2, random_state=42, stratify=labels
)

vectorizer = TfidfVectorizer(ngram_range=(1, 2))
results = compare_classifiers(X_train, y_train, X_test, y_test, vectorizer)
print("Classifier Comparison:")
print(results)
```

---

 Task 3: Hyperparameter Tuning
Optimize model hyperparameters using GridSearchCV.

```python
from sklearn.model_selection import GridSearchCV
from sklearn.pipeline import Pipeline

def tune_hyperparameters(X_train, y_train):
    """
    Tune hyperparameters for best performance.
    
    Args:
        X_train: Training texts
        y_train: Training labels
        
    Returns:
        dict: Best parameters and model
    """
     Create pipeline
    pipeline = Pipeline([
        ('vectorizer', TfidfVectorizer()),
        ('classifier', LogisticRegression(max_iter=1000))
    ])
    
     Parameter grid
    param_grid = {
        'vectorizer__max_features': [1000, 5000, 10000],
        'vectorizer__ngram_range': [(1, 1), (1, 2)],
        'vectorizer__min_df': [1, 2, 5],
        'classifier__C': [0.1, 1, 10],
        'classifier__penalty': ['l1', 'l2'],
    }
    
     Grid search
    grid_search = GridSearchCV(
        pipeline, param_grid, cv=5, scoring='accuracy', n_jobs=-1, verbose=1
    )
    
    grid_search.fit(X_train, y_train)
    
    return {
        'best_params': grid_search.best_params_,
        'best_score': grid_search.best_score_,
        'best_model': grid_search.best_estimator_
    }

 Test (commented out - takes time)
 results = tune_hyperparameters(X_train, y_train)
 print("Best Parameters:", results['best_params'])
 print("Best CV Score:", results['best_score'])

print("Note: Hyperparameter tuning can be time-consuming.")
print("Uncomment the code above to run with your dataset.")
```

---

 Task 4: Feature Selection
Implement feature selection to improve performance.

```python
from sklearn.feature_selection import SelectKBest, chi2, f_classif

def select_features(X_train, y_train, X_test, k=1000, method='chi2'):
    """
    Select top k features.
    
    Args:
        X_train: Training features
        y_train: Training labels
        X_test: Test features
        k (int): Number of features to select
        method (str): Selection method
        
    Returns:
        tuple: (X_train_selected, X_test_selected, selected_indices)
    """
    if method == 'chi2':
        selector = SelectKBest(chi2, k=k)
    elif method == 'f_classif':
        selector = SelectKBest(f_classif, k=k)
    else:
        raise ValueError(f"Unknown method: {method}")
    
    X_train_selected = selector.fit_transform(X_train, y_train)
    X_test_selected = selector.transform(X_test)
    
    selected_indices = selector.get_support(indices=True)
    
    return X_train_selected, X_test_selected, selected_indices

def compare_feature_selection(X_train, y_train, X_test, y_test, vectorizer):
    """
    Compare performance with and without feature selection.
    
    Args:
        X_train: Training texts
        y_train: Training labels
        X_test: Test texts
        y_test: Test labels
        vectorizer: Feature vectorizer
        
    Returns:
        dict: Comparison results
    """
     Without feature selection
    X_train_vec = vectorizer.fit_transform(X_train)
    X_test_vec = vectorizer.transform(X_test)
    
    model = LogisticRegression(max_iter=1000)
    model.fit(X_train_vec, y_train)
    y_pred = model.predict(X_test_vec)
    accuracy_no_selection = accuracy_score(y_test, y_pred)
    
     With feature selection
    X_train_sel, X_test_sel, selected_idx = select_features(
        X_train_vec, y_train, X_test_vec, k=1000, method='chi2'
    )
    
    model_sel = LogisticRegression(max_iter=1000)
    model_sel.fit(X_train_sel, y_train)
    y_pred_sel = model_sel.predict(X_test_sel)
    accuracy_with_selection = accuracy_score(y_test, y_pred_sel)
    
    return {
        'accuracy_no_selection': accuracy_no_selection,
        'accuracy_with_selection': accuracy_with_selection,
        'original_features': X_train_vec.shape[1],
        'selected_features': X_train_sel.shape[1],
        'reduction': (X_train_vec.shape[1] - X_train_sel.shape[1]) / X_train_vec.shape[1]
    }

 Test
vectorizer = TfidfVectorizer(ngram_range=(1, 2))
results = compare_feature_selection(X_train, y_train, X_test, y_test, vectorizer)
print("Feature Selection Comparison:")
print(f"Accuracy (no selection): {results['accuracy_no_selection']:.3f}")
print(f"Accuracy (with selection): {results['accuracy_with_selection']:.3f}")
print(f"Features: {results['original_features']} -> {results['selected_features']} ({results['reduction']:.1%} reduction)")
```

---

 Task 5: Cross-Language Classification
Build a simple multi-class classification system.

```python
from sklearn.preprocessing import LabelEncoder
from sklearn.multiclass import OneVsRestClassifier
from sklearn.preprocessing import MultiLabelBinarizer

def multiclass_classification(texts: list, labels: list):
    """
    Multi-class text classification.
    
    Args:
        texts (list): List of texts
        labels (list): List of labels
        
    Returns:
        dict: Classification results
    """
     Encode labels
    label_encoder = LabelEncoder()
    y_encoded = label_encoder.fit_transform(labels)
    
     Split
    X_train, X_test, y_train, y_test = train_test_split(
        texts, y_encoded, test_size=0.2, random_state=42, stratify=y_encoded
    )
    
     Vectorize
    vectorizer = TfidfVectorizer(ngram_range=(1, 2))
    X_train_vec = vectorizer.fit_transform(X_train)
    X_test_vec = vectorizer.transform(X_test)
    
     Train
    model = LogisticRegression(max_iter=1000, multi_class='ovr')
    model.fit(X_train_vec, y_train)
    
     Predict
    y_pred = model.predict(X_test_vec)
    
     Decode labels
    y_test_original = label_encoder.inverse_transform(y_test)
    y_pred_original = label_encoder.inverse_transform(y_pred)
    
     Metrics
    accuracy = accuracy_score(y_test, y_pred)
    
    return {
        'accuracy': accuracy,
        'classes': label_encoder.classes_,
        'n_classes': len(label_encoder.classes_),
        'confusion_matrix': confusion_matrix(y_test, y_pred)
    }

def multilabel_classification(texts: list, labels: list):
    """
    Multi-label text classification.
    
    Args:
        texts (list): List of texts
        labels (list): List of label lists
        
    Returns:
        dict: Classification results
    """
     Binarize labels
    mlb = MultiLabelBinarizer()
    y_binarized = mlb.fit_transform(labels)
    
     Split
    X_train, X_test, y_train, y_test = train_test_split(
        texts, y_binarized, test_size=0.2, random_state=42
    )
    
     Vectorize
    vectorizer = TfidfVectorizer(ngram_range=(1, 2))
    X_train_vec = vectorizer.fit_transform(X_train)
    X_test_vec = vectorizer.transform(X_test)
    
     Train with OneVsRest
    model = OneVsRestClassifier(LogisticRegression(max_iter=1000))
    model.fit(X_train_vec, y_train)
    
     Predict
    y_pred = model.predict(X_test_vec)
    
    return {
        'classes': mlb.classes_,
        'n_classes': len(mlb.classes_),
        'accuracy': accuracy_score(y_test, y_pred)
    }

 Test - Multi-class
texts_multi = [
    "Stock market hits record high",
    "New cancer treatment shows promise",
    "Champions League final tomorrow",
    "iPhone 16 rumors and leaks",
    "Election results announced",
]  20

labels_multi = ['business', 'health', 'sports', 'tech', 'politics']  20

results = multiclass_classification(texts_multi, labels_multi)
print("\nMulti-class Classification:")
print(f"Accuracy: {results['accuracy']:.3f}")
print(f"Classes: {results['classes']}")

 Test - Multi-label
texts_ml = [
    "This movie is both funny and emotional",
    "Great action scenes but poor acting",
    "Documentary about climate change and wildlife",
]  20

labels_ml = [
    ['comedy', 'drama'],
    ['action', 'drama'],
    ['documentary', 'nature'],
]  20

results_ml = multilabel_classification(texts_ml, labels_ml)
print("\nMulti-label Classification:")
print(f"Accuracy: {results_ml['accuracy']:.3f}")
print(f"Classes: {results_ml['classes']}")
```

---

 ✅ Completion Checklist

- [ ] Feature extraction methods compared
- [ ] Different classifiers evaluated
- [ ] Hyperparameter tuning implemented
- [ ] Feature selection working
- [ ] Multi-class/multi-label classification implemented
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete text classification system:

```python
class TextClassifier:
    """
    Complete text classification system.
    """
    def __init__(self, classifier='logistic'):
        """
        Initialize classifier.
        
        Args:
            classifier (str): Classifier type
        """
         Your implementation
        pass
    
    def train(self, texts: list, labels: list, tune_hyperparams=False):
        """
        Train the classifier.
        
        Args:
            texts (list): Training texts
            labels (list): Training labels
            tune_hyperparams (bool): Whether to tune hyperparameters
            
        Returns:
            training results
        """
         Your implementation
        pass
    
    def predict(self, texts: list) -> list:
        """
        Make predictions.
        
        Args:
            texts (list): Texts to classify
            
        Returns:
            list: Predictions
        """
         Your implementation
        pass
    
    def predict_proba(self, texts: list) -> list:
        """
        Get prediction probabilities.
        
        Args:
            texts (list): Texts to classify
            
        Returns:
            list: Prediction probabilities
        """
         Your implementation
        pass
    
    def evaluate(self, texts: list, labels: list) -> dict:
        """
        Evaluate classifier performance.
        
        Args:
            texts (list): Test texts
            labels (list): True labels
            
        Returns:
            dict: Evaluation metrics
        """
         Your implementation
        pass
    
    def save(self, path: str):
        """Save model."""
         Your implementation
        pass
    
    def load(self, path: str):
        """Load model."""
         Your implementation
        pass
```
