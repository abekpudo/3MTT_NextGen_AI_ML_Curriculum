 Exercise 1: Text Preprocessing Pipeline

 🎯 Objective
Build a complete text preprocessing pipeline that handles multiple text formats and cleaning scenarios.

---

 📋 Tasks

 Task 1: HTML & URL Cleaning
Create a function that removes HTML tags, URLs, and email addresses from raw text.

```python
import re

def clean_html_urls(text):
    """
    Remove HTML tags, URLs, and email addresses from text.
    
    Args:
        text (str): Raw text containing HTML, URLs, emails
        
    Returns:
        str: Cleaned text
    """
     Your implementation here
    pass

 Test cases
test_texts = [
    "<p>Visit https://example.com for more info.</p>",
    "Contact us at support@company.com or sales@company.co.uk",
    "<div><h1>Important Notice</h1><p>See https://site.org</p></div>",
]

for text in test_texts:
    cleaned = clean_html_urls(text)
    print(f"Original: {text}")
    print(f"Cleaned:  {cleaned}\n")
```

Expected Output:
- All HTML tags removed
- All URLs removed
- All email addresses removed
- Text structure preserved

---

 Task 2: Social Media Text Normalizer
Create a function specifically for cleaning social media text (tweets, posts).

```python
def normalize_social_media(text):
    """
    Normalize social media text by handling emojis, hashtags, mentions, and slang.
    
    Args:
        text (str): Social media text
        
    Returns:
        str: Normalized text suitable for analysis
    """
     Your implementation here
    pass

 Test cases
social_texts = [
    "Love this! 😊 NLP @OpenAI Check it out: https://t.co/xyz",
    "OMG this is gr8!!! Can't wait 4 more 🎉 AI ML",
    "@user I'm so excited rn!! This is lit 🔥 trending",
]

for text in social_texts:
    normalized = normalize_social_media(text)
    print(f"Original: {text}")
    print(f"Normalized: {normalized}\n")
```

Requirements:
- Convert emojis to text (e.g., 😊 → "smile_face")
- Keep hashtags but remove  symbol
- Keep mentions but remove @ symbol
- Convert common slang (gr8 → great, rn → right now, 4 → for)
- Remove excessive punctuation

---

 Task 3: Advanced Tokenization Comparison
Compare multiple tokenization strategies on the same text.

```python
import nltk
from nltk.tokenize import word_tokenize, sent_tokenize, TweetTokenizer
import spacy

nltk.download('punkt')
nltk.download('punkt_tab')

def compare_tokenizers(text):
    """
    Compare NLTK, spaCy, and Tweet tokenizers on the same text.
    
    Args:
        text (str): Input text
        
    Returns:
        dict: Dictionary with tokenization results from each tokenizer
    """
     Your implementation here
    pass

 Test text
text = "I can't wait for the conference! It's at 5:30 PM. NLP2024"

results = compare_tokenizers(text)
print(f"NLTK tokens: {results['nltk']}")
print(f"spaCy tokens: {results['spacy']}")
print(f"Tweet tokens: {results['tweet']}")
print(f"\nToken counts: NLTK={len(results['nltk'])}, spaCy={len(results['spacy'])}, tweet={len(results['tweet'])}")
```

Analysis Questions:
1. Which tokenizer handles contractions best?
2. Which tokenizer handles hashtags best?
3. Which tokenizer is most appropriate for social media text?

---

 Task 4: Stopword & Normalization Pipeline
Create a complete normalization pipeline with configurable options.

```python
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer, WordNetLemmatizer
import nltk

nltk.download('stopwords')
nltk.download('wordnet')

class TextNormalizer:
    def __init__(self, method='lemmatize', remove_stopwords=True, custom_stops=None):
        """
        Initialize text normalizer.
        
        Args:
            method (str): 'stem' or 'lemmatize'
            remove_stopwords (bool): Whether to remove stopwords
            custom_stops (list): Custom stopwords to add
        """
         Your implementation here
        pass
    
    def normalize(self, tokens):
        """
        Normalize a list of tokens.
        
        Args:
            tokens (list): List of tokens
            
        Returns:
            list: Normalized tokens
        """
         Your implementation here
        pass

 Test
normalizer_stem = TextNormalizer(method='stem')
normalizer_lemma = TextNormalizer(method='lemmatize')

tokens = ['running', 'flies', 'better', 'studies', 'went', 'the', 'is']

print("Stemmed:", normalizer_stem.normalize(tokens))
print("Lemmatized:", normalizer_lemma.normalize(tokens))
```

---

 Task 5: Regex Information Extractor
Extract structured information from unstructured text using regex.

```python
import re

def extract_entities(text):
    """
    Extract emails, phone numbers, dates, URLs, and currency from text.
    
    Args:
        text (str): Input text
        
    Returns:
        dict: Dictionary of extracted entities
    """
     Your implementation here
    pass

 Test text
text = """
Contact John at john.doe@company.com or jane@example.co.uk.
Call +1-555-123-4567 or (555) 987-6543.
Meeting scheduled for 2024-01-15 at 14:30.
Price: $1,234.56 or €999,99.
Visit https://company.com or www.example.org.
"""

entities = extract_entities(text)
print("Extracted Entities:")
print(f"Emails: {entities['emails']}")
print(f"Phones: {entities['phones']}")
print(f"Dates: {entities['dates']}")
print(f"URLs: {entities['urls']}")
print(f"Currency: {entities['currency']}")
```

---

 ✅ Completion Checklist

- [ ] HTML & URL cleaning function works
- [ ] Social media normalizer handles emojis, hashtags, mentions
- [ ] Tokenization comparison completed with analysis
- [ ] Normalization class supports both stemming and lemmatization
- [ ] Regex extractor correctly identifies all entity types
- [ ] All functions documented with docstrings
- [ ] Test cases pass for all functions

---

 🎯 Extension Challenge

Create a complete preprocessing class that combines all tasks:

```python
class NLPPreprocessor:
    """
    Complete NLP preprocessing pipeline with configurable steps.
    """
    def __init__(self, config):
        """
        Initialize with configuration dictionary.
        
        Example config:
        {
            'remove_html': True,
            'remove_urls': True,
            'normalize_social': True,
            'tokenize_method': 'spacy',   'nltk', 'spacy', 'tweet'
            'normalize_method': 'lemmatize',   'stem', 'lemmatize'
            'remove_stopwords': True,
            'custom_stopwords': ['additional', 'words'],
            'lowercase': True
        }
        """
         Your implementation
        pass
    
    def preprocess(self, text):
        """
        Apply full preprocessing pipeline to text.
        
        Returns:
            dict: Dictionary with intermediate and final results
        """
         Your implementation
        pass
```

---
