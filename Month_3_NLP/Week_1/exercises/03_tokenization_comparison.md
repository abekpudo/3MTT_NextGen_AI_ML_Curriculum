 Exercise 3: Advanced Tokenization & Subword Methods

 🎯 Objective
Explore and compare modern tokenization methods including subword tokenization (BPE, WordPiece).

---

 📋 Tasks

 Task 1: Character vs Word vs Subword Tokenization
Compare different tokenization granularities.

```python
from collections import Counter
import re

def tokenize_char(text):
    """Character-level tokenization."""
    return list(text)

def tokenize_word(text):
    """Word-level tokenization with simple split."""
    return re.findall(r'\w+', text.lower())

def tokenize_subword_bpe(text, vocab_size=100):
    """
    Simplified Byte Pair Encoding (BPE) implementation.
    
    Args:
        text (str): Input text
        vocab_size (int): Target vocabulary size
        
    Returns:
        list: Subword tokens
    """
     Simplified BPE - in practice, use HuggingFace tokenizers
     This is a conceptual implementation
    words = re.findall(r'\w+', text.lower())
    chars = [list(word) for word in words]
    
     Start with character-level
    vocab = set(''.join(words))
    
     Iteratively merge most common pairs
     (simplified - real BPE is more complex)
    return words   Placeholder

 Test text
text = "Natural language processing is fascinating"

print("Character tokens:", tokenize_char(text))
print(f"Count: {len(tokenize_char(text))}")

print("\nWord tokens:", tokenize_word(text))
print(f"Count: {len(tokenize_word(text))}")

print("\nSubword (BPE - simplified):", tokenize_subword_bpe(text))
```

Analysis Questions:
1. What is the vocabulary size for each method?
2. Which method handles out-of-vocabulary (OOV) words?
3. What is the trade-off between vocabulary size and sequence length?

---

 Task 2: HuggingFace Tokenizer Comparison
Compare different transformer tokenizers.

```python
from transformers import AutoTokenizer

def compare_transformer_tokenizers(text):
    """
    Compare different HuggingFace tokenizers.
    
    Args:
        text (str): Input text
        
    Returns:
        dict: Tokenization results from different models
    """
    tokenizers = {
        'BERT': AutoTokenizer.from_pretrained('bert-base-uncased'),
        'GPT-2': AutoTokenizer.from_pretrained('gpt2'),
        'RoBERTa': AutoTokenizer.from_pretrained('roberta-base'),
        'DistilBERT': AutoTokenizer.from_pretrained('distilbert-base-uncased'),
    }
    
    results = {}
    for name, tokenizer in tokenizers.items():
        tokens = tokenizer.tokenize(text)
        ids = tokenizer.convert_tokens_to_ids(tokens)
        
        results[name] = {
            'tokens': tokens,
            'token_count': len(tokens),
            'token_ids': ids[:10]   First 10 IDs
        }
    
    return results

 Test texts
test_texts = [
    "Natural language processing is fascinating",
    "The microprocessor was invented in the 1970s",
    "COVID-19 vaccine research has progressed rapidly",
]

for text in test_texts:
    print(f"\nText: {text}")
    results = compare_transformer_tokenizers(text)
    for name, result in results.items():
        print(f"  {name}: {result['token_count']} tokens")
        print(f"    Tokens: {result['tokens'][:5]}...")
```

Analysis:
1. Which tokenizer produces the fewest tokens?
2. How do tokenizers handle special characters and numbers?
3. Which tokenizer handles rare words best?

---

 Task 3: Subword Statistics
Analyze subword tokenization properties.

```python
def analyze_tokenizer(tokenizer, text):
    """
    Analyze tokenizer statistics.
    
    Args:
        tokenizer: HuggingFace tokenizer
        text (str): Input text
        
    Returns:
        dict: Statistics
    """
    tokens = tokenizer.tokenize(text)
    
     Count subword types
    subword_types = {
        'whole_words': sum(1 for t in tokens if not t.startswith('') and not t.startswith('Ġ')),
        'subwords': sum(1 for t in tokens if t.startswith('') or t.startswith('Ġ')),
        'special_tokens': sum(1 for t in tokens if t.startswith('[') or t in ['<|endoftext|>', '<pad>'])
    }
    
     Token lengths
    lengths = [len(t.replace('', '').replace('Ġ', '')) for t in tokens]
    
    return {
        'total_tokens': len(tokens),
        'subword_types': subword_types,
        'avg_token_length': sum(lengths) / len(lengths) if lengths else 0,
        'max_token_length': max(lengths) if lengths else 0,
        'tokens': tokens
    }

 Test
tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased')
texts = [
    "The quick brown fox jumps over the lazy dog",
    "Supercalifragilisticexpialidocious",
    "Artificial intelligence transforms industries",
]

for text in texts:
    stats = analyze_tokenizer(tokenizer, text)
    print(f"\nText: {text}")
    print(f"Total tokens: {stats['total_tokens']}")
    print(f"Subword types: {stats['subword_types']}")
    print(f"Avg token length: {stats['avg_token_length']:.2f}")
    print(f"Tokens: {stats['tokens']}")
```

---

 Task 4: Custom Tokenizer
Create a domain-specific tokenizer.

```python
class DomainTokenizer:
    """
    Custom tokenizer for specific domain (e.g., medical, legal).
    """
    def __init__(self, domain_terms=None):
        """
        Initialize with domain-specific vocabulary.
        
        Args:
            domain_terms (list): List of domain-specific terms to keep as single tokens
        """
        self.domain_terms = set(domain_terms or [])
        self.domain_terms_lower = {t.lower() for t in self.domain_terms}
    
    def tokenize(self, text):
        """
        Tokenize with domain awareness.
        
        Args:
            text (str): Input text
            
        Returns:
            list: Tokens
        """
         Your implementation here
         1. Check for domain terms (keep as single tokens)
         2. Handle standard tokenization for other text
         3. Handle special cases (hyphens, etc.)
        pass

 Example: Medical domain tokenizer
medical_terms = [
    'hypertension', 'myocardial infarction', 'diabetes mellitus',
    'chronic obstructive pulmonary disease', 'cardiovascular'
]

medical_tokenizer = DomainTokenizer(domain_terms=medical_terms)

text = "Patient has hypertension and diabetes mellitus."
tokens = medical_tokenizer.tokenize(text)
print(f"Medical tokenizer: {tokens}")

 Compare with standard tokenization
standard_tokens = tokenize_word(text)
print(f"Standard tokenizer: {standard_tokens}")
```

---

 Task 5: Tokenization Impact on Downstream Tasks
Test how different tokenizers affect model performance.

```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

def tokenizer_experiment(corpus, labels):
    """
    Test impact of different tokenization strategies.
    
    Args:
        corpus (list): List of documents
        labels (list): List of labels
        
    Returns:
        dict: Results for each tokenizer
    """
    X_train, X_test, y_train, y_test = train_test_split(
        corpus, labels, test_size=0.2, random_state=42
    )
    
    tokenizers = {
        'simple': lambda x: x.split(),
        'character': lambda x: list(x),
        'word_regex': lambda x: re.findall(r'\w+', x.lower()),
    }
    
    results = {}
    
    for name, tokenizer_func in tokenizers.items():
        vectorizer = CountVectorizer(tokenizer=tokenizer_func)
        X_train_vec = vectorizer.fit_transform(X_train)
        X_test_vec = vectorizer.transform(X_test)
        
        model = MultinomialNB()
        model.fit(X_train_vec, y_train)
        y_pred = model.predict(X_test_vec)
        
        results[name] = {
            'accuracy': accuracy_score(y_test, y_pred),
            'vocab_size': len(vectorizer.vocabulary_),
            'avg_doc_length': X_train_vec.sum(axis=1).mean()
        }
    
    return results

 Test with sample data
corpus = [f"This is {'positive' if i % 2 == 0 else 'negative'} text" for i in range(100)]
labels = [1 if i % 2 == 0 else 0 for i in range(100)]

results = tokenizer_experiment(corpus, labels)
print("Tokenizer Experiment Results:")
for name, metrics in results.items():
    print(f"{name}:")
    print(f"  Accuracy: {metrics['accuracy']:.3f}")
    print(f"  Vocab Size: {metrics['vocab_size']}")
    print(f"  Avg Doc Length: {metrics['avg_doc_length']:.2f}")
```

---

 ✅ Completion Checklist

- [ ] Character/word/subword tokenization compared
- [ ] HuggingFace tokenizers analyzed
- [ ] Subword statistics calculated
- [ ] Custom domain tokenizer created
- [ ] Tokenization impact on downstream tasks evaluated
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Implement a complete tokenizer training pipeline:

```python
class TokenizerTrainer:
    """
    Train custom tokenizers from corpus data.
    """
    def __init__(self, vocab_size=30000, min_frequency=2):
        """
        Initialize tokenizer trainer.
        
        Args:
            vocab_size (int): Target vocabulary size
            min_frequency (int): Minimum frequency for tokens
        """
         Your implementation
        pass
    
    def train_bpe(self, corpus):
        """
        Train BPE tokenizer from corpus.
        
        Args:
            corpus (list): List of documents
            
        Returns:
            trained tokenizer
        """
         Your implementation
        pass
    
    def train_wordpiece(self, corpus):
        """
        Train WordPiece tokenizer from corpus.
        
        Args:
            corpus (list): List of documents
            
        Returns:
            trained tokenizer
        """
         Your implementation
        pass
    
    def save_tokenizer(self, tokenizer, path):
        """
        Save trained tokenizer.
        
        Args:
            tokenizer: Trained tokenizer
            path (str): Save path
        """
         Your implementation
        pass
    
    def load_tokenizer(self, path):
        """
        Load trained tokenizer.
        
        Args:
            path (str): Load path
            
        Returns:
            loaded tokenizer
        """
         Your implementation
        pass
```
