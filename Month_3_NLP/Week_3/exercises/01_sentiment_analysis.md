 Exercise 1: Sentiment Analysis Systems

 🎯 Objective
Build comprehensive sentiment analysis systems using multiple approaches.

---

 📋 Tasks

 Task 1: Lexicon-Based Sentiment Analysis
Implement sentiment analysis using VADER and TextBlob.

```python
from textblob import TextBlob
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import pandas as pd

def compare_lexicon_methods(texts: list) -> pd.DataFrame:
    """
    Compare VADER and TextBlob sentiment analysis.
    
    Args:
        texts (list): List of texts to analyze
        
    Returns:
        DataFrame: Comparison results
    """
    analyzer = SentimentIntensityAnalyzer()
    
    results = []
    for text in texts:
         TextBlob
        blob = TextBlob(text)
        tb_polarity = blob.sentiment.polarity
        tb_subjectivity = blob.sentiment.subjectivity
        tb_sentiment = "Positive" if tb_polarity > 0 else "Negative" if tb_polarity < 0 else "Neutral"
        
         VADER
        vader_scores = analyzer.polarity_scores(text)
        vader_compound = vader_scores['compound']
        vader_sentiment = "Positive" if vader_compound >= 0.05 else "Negative" if vader_compound <= -0.05 else "Neutral"
        
        results.append({
            'text': text,
            'textblob_polarity': tb_polarity,
            'textblob_subjectivity': tb_subjectivity,
            'textblob_sentiment': tb_sentiment,
            'vader_compound': vader_compound,
            'vader_sentiment': vader_sentiment
        })
    
    return pd.DataFrame(results)

 Test
texts = [
    "I absolutely love this product! Best purchase ever!",
    "Terrible quality, complete waste of money.",
    "It's okay, nothing special but does the job.",
    "Love the design, but battery life could be better.",
    "Would not recommend. Broke after one week.",
]

results = compare_lexicon_methods(texts)
print(results)
```

---

 Task 2: Custom Sentiment Lexicon
Build a domain-specific sentiment lexicon.

```python
from collections import defaultdict

class CustomSentimentLexicon:
    """
    Custom sentiment lexicon builder.
    """
    def __init__(self):
        """Initialize with empty lexicon."""
        self.lexicon = defaultdict(float)
        self.negation_words = {'not', 'no', 'never', 'none', 'nobody', 'nothing', 'neither', 'nowhere'}
    
    def add_word(self, word: str, sentiment: float):
        """
        Add word to lexicon.
        
        Args:
            word (str): Word to add
            sentiment (float): Sentiment score (-1 to 1)
        """
        self.lexicon[word.lower()] = sentiment
    
    def load_from_file(self, filepath: str):
        """
        Load lexicon from file (format: word,score).
        
        Args:
            filepath (str): Path to lexicon file
        """
        with open(filepath, 'r') as f:
            for line in f:
                parts = line.strip().split(',')
                if len(parts) == 2:
                    word, score = parts
                    self.add_word(word, float(score))
    
    def analyze(self, text: str) -> dict:
        """
        Analyze sentiment of text.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Sentiment analysis results
        """
        words = text.lower().split()
        scores = []
        
        negation = False
        for word in words:
             Check for negation
            if word in self.negation_words:
                negation = True
                continue
            
             Get word score
            score = self.lexicon.get(word, 0)
            
             Flip sentiment if negated
            if negation:
                score = -1
                negation = False
            
            scores.append(score)
        
        if not scores:
            return {'sentiment': 0, 'label': 'Neutral'}
        
        avg_sentiment = sum(scores) / len(scores)
        
        if avg_sentiment > 0.1:
            label = "Positive"
        elif avg_sentiment < -0.1:
            label = "Negative"
        else:
            label = "Neutral"
        
        return {
            'sentiment': avg_sentiment,
            'label': label,
            'word_count': len(words),
            'words_with_sentiment': len(scores)
        }

 Test
lexicon = CustomSentimentLexicon()

 Add domain-specific words
lexicon.add_word('amazing', 0.9)
lexicon.add_word('terrible', -0.9)
lexicon.add_word('excellent', 0.85)
lexicon.add_word('awful', -0.85)
lexicon.add_word('good', 0.6)
lexicon.add_word('bad', -0.6)
lexicon.add_word('okay', 0.1)
lexicon.add_word('great', 0.8)

texts = [
    "This product is amazing!",
    "This is not good at all",
    "The quality is terrible",
    "It's okay but could be better",
]

for text in texts:
    result = lexicon.analyze(text)
    print(f"'{text}' -> {result['label']} ({result['sentiment']:.3f})")
```

---

 Task 3: Aspect-Based Sentiment Analysis
Implement aspect-based sentiment analysis.

```python
import re
from typing import List, Dict

class AspectBasedSentiment:
    """
    Aspect-based sentiment analysis.
    """
    def __init__(self, sentiment_analyzer):
        """
        Initialize with sentiment analyzer.
        
        Args:
            sentiment_analyzer: Sentiment analysis function
        """
        self.sentiment_analyzer = sentiment_analyzer
        self.aspects = {
            'battery': ['battery', 'power', 'charge', 'life'],
            'screen': ['screen', 'display', 'resolution'],
            'camera': ['camera', 'photo', 'picture', 'video'],
            'performance': ['performance', 'speed', 'fast', 'slow'],
            'price': ['price', 'cost', 'expensive', 'cheap', 'value'],
        }
    
    def extract_aspects(self, text: str) -> Dict[str, List[str]]:
        """
        Extract aspects from text.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Aspect mentions
        """
        text_lower = text.lower()
        aspects_found = {}
        
        for aspect, keywords in self.aspects.items():
            for keyword in keywords:
                if keyword in text_lower:
                    if aspect not in aspects_found:
                        aspects_found[aspect] = []
                    aspects_found[aspect].append(keyword)
        
        return aspects_found
    
    def analyze_aspects(self, text: str) -> Dict[str, Dict]:
        """
        Analyze sentiment for each aspect.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Aspect sentiment analysis
        """
        aspects = self.extract_aspects(text)
        results = {}
        
        for aspect, keywords in aspects.items():
             Extract sentences mentioning this aspect
            sentences = self._extract_aspect_sentences(text, aspect)
            
             Analyze sentiment for each sentence
            sentiments = []
            for sentence in sentences:
                sentiment = self.sentiment_analyzer(sentence)
                sentiments.append(sentiment)
            
             Average sentiment
            if sentiments:
                avg_sentiment = sum(sentiments) / len(sentiments)
                label = "Positive" if avg_sentiment > 0.1 else "Negative" if avg_sentiment < -0.1 else "Neutral"
                
                results[aspect] = {
                    'sentiment': avg_sentiment,
                    'label': label,
                    'sentences': sentences,
                    'keywords': keywords
                }
        
        return results
    
    def _extract_aspect_sentences(self, text: str, aspect: str) -> List[str]:
        """
        Extract sentences mentioning an aspect.
        
        Args:
            text (str): Input text
            aspect (str): Aspect name
            
        Returns:
            list: Sentences
        """
        keywords = self.aspects[aspect]
        sentences = re.split(r'[.!?]', text)
        
        aspect_sentences = []
        for sentence in sentences:
            if any(keyword in sentence.lower() for keyword in keywords):
                aspect_sentences.append(sentence.strip())
        
        return aspect_sentences

 Test
def simple_sentiment(text: str) -> float:
    """Simple sentiment function for demo."""
    positive_words = ['good', 'great', 'excellent', 'amazing', 'love']
    negative_words = ['bad', 'terrible', 'awful', 'hate', 'poor']
    
    text_lower = text.lower()
    score = 0
    
    for word in positive_words:
        score += text_lower.count(word)  0.5
    
    for word in negative_words:
        score -= text_lower.count(word)  0.5
    
    return score

absa = AspectBasedSentiment(simple_sentiment)

review = """
The battery life is amazing, lasts all day. The screen is good but the camera is terrible.
The price is reasonable for the quality. Performance is excellent overall.
"""

results = absa.analyze_aspects(review)
print("Aspect-Based Sentiment Analysis:")
for aspect, result in results.items():
    print(f"\n{aspect}:")
    print(f"  Sentiment: {result['label']} ({result['sentiment']:.3f})")
    print(f"  Keywords: {result['keywords']}")
    print(f"  Sentences: {result['sentences']}")
```

---

 Task 4: Sentiment Analysis with Negation Handling
Implement sophisticated negation handling.

```python
import re

class NegationAwareSentiment:
    """
    Sentiment analysis with negation handling.
    """
    def __init__(self, lexicon: dict):
        """
        Initialize with sentiment lexicon.
        
        Args:
            lexicon (dict): Word to sentiment mapping
        """
        self.lexicon = lexicon
        self.negation_words = {'not', 'no', 'never', 'none', 'nobody', 'nothing', 
                              'neither', 'nowhere', 'hardly', 'barely', 'scarcely'}
        self.negation_scope = 3   Words affected by negation
    
    def analyze(self, text: str) -> dict:
        """
        Analyze sentiment with negation handling.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Sentiment analysis
        """
        tokens = text.lower().split()
        scores = []
        negation_active = False
        negation_counter = 0
        
        for token in tokens:
             Check for negation
            if token in self.negation_words:
                negation_active = True
                negation_counter = self.negation_scope
                continue
            
             Get word sentiment
            word_score = self.lexicon.get(token, 0)
            
             Apply negation if active
            if negation_active and word_score != 0:
                word_score = -1
                negation_counter -= 1
                if negation_counter <= 0:
                    negation_active = False
            
            scores.append(word_score)
        
        if not scores:
            return {'sentiment': 0, 'label': 'Neutral'}
        
        avg_sentiment = sum(scores) / len(scores)
        
        if avg_sentiment > 0.1:
            label = "Positive"
        elif avg_sentiment < -0.1:
            label = "Negative"
        else:
            label = "Neutral"
        
        return {
            'sentiment': avg_sentiment,
            'label': label
        }

 Test
lexicon = {
    'good': 0.6,
    'bad': -0.6,
    'great': 0.8,
    'terrible': -0.8,
    'happy': 0.7,
    'sad': -0.7,
}

analyzer = NegationAwareSentiment(lexicon)

texts = [
    "This product is good",
    "This product is not good",
    "This is not a bad product",
    "This is not at all good",
    "This is great",
]

for text in texts:
    result = analyzer.analyze(text)
    print(f"'{text}' -> {result['label']} ({result['sentiment']:.3f})")
```

---

 Task 5: Sentiment Analysis Evaluation
Evaluate sentiment analysis systems.

```python
from sklearn.metrics import accuracy_score, precision_recall_fscore_support, confusion_matrix
import numpy as np

def evaluate_sentiment(predictions: list, true_labels: list) -> dict:
    """
    Evaluate sentiment analysis performance.
    
    Args:
        predictions (list): Predicted labels
        true_labels (list): True labels
        
    Returns:
        dict: Evaluation metrics
    """
     Convert labels to numeric
    label_map = {'Negative': 0, 'Neutral': 1, 'Positive': 2}
    
    pred_numeric = [label_map.get(p, 1) for p in predictions]
    true_numeric = [label_map.get(t, 1) for t in true_labels]
    
     Calculate metrics
    accuracy = accuracy_score(true_numeric, pred_numeric)
    
    precision, recall, f1, support = precision_recall_fscore_support(
        true_numeric, pred_numeric, average='weighted'
    )
    
     Per-class metrics
    precision_per, recall_per, f1_per, support_per = precision_recall_fscore_support(
        true_numeric, pred_numeric, average=None
    )
    
     Confusion matrix
    cm = confusion_matrix(true_numeric, pred_numeric)
    
    return {
        'accuracy': accuracy,
        'precision': precision,
        'recall': recall,
        'f1': f1,
        'per_class': {
            'Negative': {'precision': precision_per[0], 'recall': recall_per[0], 'f1': f1_per[0]},
            'Neutral': {'precision': precision_per[1], 'recall': recall_per[1], 'f1': f1_per[1]},
            'Positive': {'precision': precision_per[2], 'recall': recall_per[2], 'f1': f1_per[2]},
        },
        'confusion_matrix': cm
    }

 Test
predictions = ['Positive', 'Negative', 'Neutral', 'Positive', 'Negative']
true_labels = ['Positive', 'Negative', 'Positive', 'Positive', 'Negative']

results = evaluate_sentiment(predictions, true_labels)
print("Sentiment Analysis Evaluation:")
print(f"Accuracy: {results['accuracy']:.3f}")
print(f"Precision: {results['precision']:.3f}")
print(f"Recall: {results['recall']:.3f}")
print(f"F1: {results['f1']:.3f}")
print("\nPer-class metrics:")
for label, metrics in results['per_class'].items():
    print(f"  {label}: P={metrics['precision']:.3f}, R={metrics['recall']:.3f}, F1={metrics['f1']:.3f}")
```

---

 ✅ Completion Checklist

- [ ] Lexicon-based sentiment analysis compared
- [ ] Custom sentiment lexicon built
- [ ] Aspect-based sentiment analysis implemented
- [ ] Negation handling working
- [ ] Evaluation system created
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete sentiment analysis pipeline:

```python
class SentimentAnalysisPipeline:
    """
    Complete sentiment analysis pipeline.
    """
    def __init__(self, method='ensemble'):
        """
        Initialize pipeline.
        
        Args:
            method (str): 'lexicon', 'ml', 'dl', or 'ensemble'
        """
         Your implementation
        pass
    
    def analyze(self, text: str) -> dict:
        """
        Analyze sentiment using selected method.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Sentiment analysis results
        """
         Your implementation
        pass
    
    def analyze_batch(self, texts: list) -> list:
        """
        Analyze multiple texts.
        
        Args:
            texts (list): List of texts
            
        Returns:
            list: Results for each text
        """
         Your implementation
        pass
    
    def analyze_aspects(self, text: str) -> dict:
        """
        Perform aspect-based sentiment analysis.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Aspect sentiments
        """
         Your implementation
        pass
    
    def train(self, texts: list, labels: list):
        """
        Train ML/DL model.
        
        Args:
            texts (list): Training texts
            labels (list): Training labels
        """
         Your implementation
        pass
```
