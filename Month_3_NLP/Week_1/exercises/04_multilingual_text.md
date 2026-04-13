 Exercise 4: Multilingual Text Processing

 🎯 Objective
Handle text in multiple languages and implement multilingual preprocessing pipelines.

---

 📋 Tasks

 Task 1: Language Detection
Implement basic language detection using character n-grams.

```python
from collections import Counter
import re

def extract_ngrams(text, n=3):
    """
    Extract character n-grams from text.
    
    Args:
        text (str): Input text
        n (int): N-gram size
        
    Returns:
        list: List of n-grams
    """
    text = re.sub(r'\s+', '', text.lower())
    return [text[i:i+n] for i in range(len(text)-n+1)]

def detect_language(text, language_profiles):
    """
    Detect language using n-gram profile matching.
    
    Args:
        text (str): Input text
        language_profiles (dict): Dictionary of language n-gram profiles
        
    Returns:
        str: Detected language
    """
     Get n-grams of text
    text_ngrams = extract_ngrams(text)
    text_profile = Counter(text_ngrams)
    
    best_match = None
    best_score = -1
    
    for lang, lang_profile in language_profiles.items():
         Calculate overlap score
        common = set(text_profile.keys()) & set(lang_profile.keys())
        score = sum(text_profile[n]  lang_profile[n] for n in common)
        
        if score > best_score:
            best_score = score
            best_match = lang
    
    return best_match

 Sample language profiles (simplified)
language_profiles = {
    'english': Counter(extract_ngrams("the quick brown fox jumps over the lazy dog")),
    'french': Counter(extract_ngrams("le renard brun rapide saute par-dessus le chien paresseux")),
    'spanish': Counter(extract_ngrams("el zorro marrón rápido salta sobre el perro perezoso")),
    'german': Counter(extract_ngrams("der schnelle braune Fuchs springt über den faulen Hund")),
}

 Test
test_texts = [
    "This is an English sentence",
    "Ceci est une phrase française",
    "Esta es una oración en español",
    "Dies ist ein deutscher Satz",
]

for text in test_texts:
    detected = detect_language(text, language_profiles)
    print(f"'{text}' -> {detected}")
```

Analysis Questions:
1. How does n-gram size affect detection accuracy?
2. What happens with short texts?
3. How would you handle code-switching (mixed languages)?

---

 Task 2: Multilingual Stopword Handling
Create a multilingual stopword removal system.

```python
from nltk.corpus import stopwords
import nltk

nltk.download('stopwords')

class MultilingualStopwordRemover:
    """
    Remove stopwords for multiple languages.
    """
    def __init__(self, languages=['english', 'french', 'spanish', 'german']):
        """
        Initialize with supported languages.
        
        Args:
            languages (list): List of languages
        """
        self.stopwords = {}
        for lang in languages:
            try:
                self.stopwords[lang] = set(stopwords.words(lang))
            except:
                print(f"Warning: {lang} stopwords not available")
    
    def remove_stopwords(self, text, language='english'):
        """
        Remove stopwords from text.
        
        Args:
            text (str): Input text
            language (str): Language of text
            
        Returns:
            str: Text with stopwords removed
        """
        if language not in self.stopwords:
            print(f"Warning: {language} not supported, using English")
            language = 'english'
        
        tokens = text.split()
        filtered = [t for t in tokens if t.lower() not in self.stopwords[language]]
        return ' '.join(filtered)
    
    def detect_and_remove(self, text):
        """
        Detect language and remove stopwords.
        
        Args:
            text (str): Input text
            
        Returns:
            tuple: (cleaned_text, detected_language)
        """
         Use language detection from Task 1
        detected = detect_language(text, language_profiles)
        cleaned = self.remove_stopwords(text, detected)
        return cleaned, detected

 Test
remover = MultilingualStopwordRemover()

texts = [
    "This is a test sentence in English",
    "Ceci est une phrase de test en français",
    "Esta es una oración de prueba en español",
]

for text in texts:
    cleaned, lang = remover.detect_and_remove(text)
    print(f"Original ({lang}): {text}")
    print(f"Cleaned: {cleaned}\n")
```

---

 Task 3: Unicode Normalization
Handle different Unicode representations.

```python
import unicodedata

def normalize_unicode(text, form='NFC'):
    """
    Normalize Unicode text.
    
    Args:
        text (str): Input text
        form (str): Normalization form ('NFC', 'NFD', 'NFKC', 'NFKD')
        
    Returns:
        str: Normalized text
    """
    return unicodedata.normalize(form, text)

def compare_normalizations(text):
    """
    Compare different normalization forms.
    
    Args:
        text (str): Input text
        
    Returns:
        dict: Results for each form
    """
    forms = ['NFC', 'NFD', 'NFKC', 'NFKD']
    results = {}
    
    for form in forms:
        normalized = normalize_unicode(text, form)
        results[form] = {
            'text': normalized,
            'length': len(normalized),
            'bytes': normalized.encode('utf-8')
        }
    
    return results

 Test with text that has different representations
text = "café naïve résumé"   Can have different Unicode representations
text_with_combining = "cafe\u0301 nai\u0308ve re\u0301sume\u0301"   Using combining characters

print("Original:", text)
print("With combining chars:", text_with_combining)

print("\nNormalization comparison:")
results = compare_normalizations(text_with_combining)
for form, result in results.items():
    print(f"{form}: {result['text']} (len={result['length']})")
```

Analysis Questions:
1. What is the difference between NFC and NFD?
2. When would you use NFKC vs NFC?
3. Why is Unicode normalization important for NLP?

---

 Task 4: Multilingual Tokenization
Compare tokenization across languages.

```python
import spacy

def multilingual_tokenization(text, language='en'):
    """
    Tokenize text in specified language.
    
    Args:
        text (str): Input text
        language (str): Language code ('en', 'fr', 'es', 'de')
        
    Returns:
        list: Tokens
    """
     Download models if needed
     python -m spacy download en_core_web_sm
     python -m spacy download fr_core_news_sm
     python -m spacy download es_core_news_sm
     python -m spacy download de_core_news_sm
    
    models = {
        'en': 'en_core_web_sm',
        'fr': 'fr_core_news_sm',
        'es': 'es_core_news_sm',
        'de': 'de_core_news_sm',
    }
    
    if language not in models:
        raise ValueError(f"Language {language} not supported")
    
    try:
        nlp = spacy.load(models[language])
        doc = nlp(text)
        return [token.text for token in doc]
    except:
        print(f"Warning: Model for {language} not installed")
        return text.split()

 Test multilingual texts
multilingual_texts = {
    'en': "The quick brown fox jumps over the lazy dog",
    'fr': "Le renard brun rapide saute par-dessus le chien paresseux",
    'es': "El zorro marrón rápido salta sobre el perro perezoso",
    'de': "Der schnelle braune Fuchs springt über den faulen Hund",
}

for lang, text in multilingual_texts.items():
    try:
        tokens = multilingual_tokenization(text, lang)
        print(f"{lang}: {len(tokens)} tokens")
        print(f"  Tokens: {tokens[:5]}...")
    except Exception as e:
        print(f"{lang}: Error - {e}")
```

---

 Task 5: Multilingual Sentiment Analysis
Apply preprocessing to multilingual sentiment analysis.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

class MultilingualPreprocessor:
    """
    Preprocess text for multilingual NLP tasks.
    """
    def __init__(self, languages=['english']):
        """
        Initialize with supported languages.
        
        Args:
            languages (list): List of languages
        """
        self.languages = languages
        self.stopword_remover = MultilingualStopwordRemover(languages)
    
    def preprocess(self, text, language='english'):
        """
        Apply full preprocessing pipeline.
        
        Args:
            text (str): Input text
            language (str): Language of text
            
        Returns:
            str: Preprocessed text
        """
         Normalize Unicode
        text = normalize_unicode(text)
        
         Remove stopwords
        text = self.stopword_remover.remove_stopwords(text, language)
        
         Lowercase
        text = text.lower()
        
         Remove extra whitespace
        text = ' '.join(text.split())
        
        return text

 Test with multilingual dataset
corpus = [
    ("This is great!", 'en', 1),
    ("This is terrible!", 'en', 0),
    ("C'est excellent!", 'fr', 1),
    ("C'est terrible!", 'fr', 0),
    ("¡Esto es genial!", 'es', 1),
    ("¡Esto es terrible!", 'es', 0),
]

 Preprocess
preprocessor = MultilingualPreprocessor(['english', 'french', 'spanish'])
preprocessed = [(preprocessor.preprocess(text, lang), label) for text, lang, label in corpus]

 Train simple classifier
texts = [p[0] for p in preprocessed]
labels = [p[1] for p in preprocessed]

vectorizer = TfidfVectorizer()
X = vectorizer.fit_transform(texts)

model = MultinomialNB()
model.fit(X, labels)

 Test
test_text = "This is amazing"
test_preprocessed = preprocessor.preprocess(test_text, 'en')
test_vector = vectorizer.transform([test_preprocessed])
prediction = model.predict(test_vector)

print(f"Text: {test_text}")
print(f"Preprocessed: {test_preprocessed}")
print(f"Prediction: {'Positive' if prediction[0] == 1 else 'Negative'}")
```

---

 ✅ Completion Checklist

- [ ] Language detection implemented
- [ ] Multilingual stopword removal working
- [ ] Unicode normalization applied
- [ ] Multilingual tokenization tested
- [ ] Multilingual preprocessing pipeline created
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete multilingual NLP pipeline:

```python
class MultilingualNLPPipeline:
    """
    Complete multilingual NLP processing pipeline.
    """
    def __init__(self, supported_languages=['en', 'fr', 'es', 'de']):
        """
        Initialize pipeline.
        
        Args:
            supported_languages (list): List of language codes
        """
         Your implementation
        pass
    
    def detect_language(self, text):
        """
        Detect language of input text.
        
        Args:
            text (str): Input text
            
        Returns:
            str: Language code
        """
         Your implementation
        pass
    
    def preprocess(self, text, language=None):
        """
        Preprocess text with language-specific rules.
        
        Args:
            text (str): Input text
            language (str): Language code (auto-detect if None)
            
        Returns:
            dict: Preprocessing results
        """
         Your implementation
        pass
    
    def tokenize(self, text, language=None):
        """
        Tokenize text with language-specific tokenizer.
        
        Args:
            text (str): Input text
            language (str): Language code
            
        Returns:
            list: Tokens
        """
         Your implementation
        pass
    
    def vectorize(self, texts, language=None):
        """
        Vectorize texts for ML tasks.
        
        Args:
            texts (list): List of texts
            language (str): Language code
            
        Returns:
            array: Feature matrix
        """
         Your implementation
        pass
    
    def analyze(self, text):
        """
        Perform complete analysis.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Complete analysis results
        """
         Your implementation
        pass
```
