 Week 1: NLP Fundamentals & Text Preprocessing

Duration: 5 days | Theme: Foundation of Text Processing | Difficulty: Beginner-Intermediate  
Prerequisite: Complete Month 2 (ML Techniques)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Understand the NLP pipeline and its applications
- ✅ Master text preprocessing techniques (cleaning, normalization)
- ✅ Implement multiple tokenization strategies
- ✅ Apply stemming and lemmatization effectively
- ✅ Use regex for text pattern matching and extraction
- ✅ Convert text to numerical features (BoW, TF-IDF)
- ✅ Build a complete text preprocessing pipeline

---

 📖 Detailed Daily Schedule

 Day 1: NLP Pipeline & Text Basics
Lecture: [01_nlp_introduction.md](./lecture_notes/01_nlp_introduction.md) | [02_text_preprocessing.md](./lecture_notes/02_text_preprocessing.md)

Topics:
- What is NLP? Applications and challenges
- The NLP pipeline: Raw Text → Clean Text → Tokens → Features → Model
- Text encoding (ASCII, UTF-8, Unicode)
- Text cleaning: HTML tags, URLs, special characters
- Case normalization and whitespace handling

Hands-On:
```python
import re
import string

 Sample raw text
raw_text = """
<html><body>
Check out https://example.com! Email us at test@email.com
Price: $1,234.56!!! Special offer limited 😊
</body></html>
"""

 Remove HTML tags
clean_html = re.sub(r'<[^>]+>', '', raw_text)
print("After HTML removal:", clean_html[:100])

 Remove URLs
clean_urls = re.sub(r'http\S+|www\S+|https\S+', '', clean_html, flags=re.MULTILINE)

 Remove email addresses
clean_email = re.sub(r'\S+@\S+', '', clean_urls)

 Remove special characters but keep sentence structure
clean_special = re.sub(r'[^\w\s\.]', '', clean_email)

 Normalize whitespace
clean_ws = ' '.join(clean_special.split())

 Convert to lowercase
clean_lower = clean_ws.lower()

print("Final cleaned text:")
print(clean_lower)

 Text statistics
print(f"\nCharacters: {len(raw_text)}")
print(f"After cleaning: {len(clean_lower)}")
print(f"Reduction: {(1 - len(clean_lower)/len(raw_text))100:.1f}%")
```

Practice:
- [ ] Create text cleaner for 5 different text sources (tweets, news, emails)
- [ ] Handle emojis, HTML, URLs, mentions
- [ ] Implement language detection basics
- [ ] Build text quality metrics

---

 Day 2: Tokenization Strategies
Lecture: [03_tokenization.md](./lecture_notes/03_tokenization.md)

Topics:
- Word tokenization (whitespace, punctuation)
- Sentence tokenization
- Subword tokenization (BPE, WordPiece)
- Character tokenization
- NLTK, spaCy, and HuggingFace tokenizers

Hands-On:
```python
import nltk
from nltk.tokenize import word_tokenize, sent_tokenize
import spacy

 Download required data
nltk.download('punkt')
nltk.download('punkt_tab')

 Sample text
text = "Natural Language Processing (NLP) is fascinating! It enables machines to understand human language. Can't wait to learn more."

 NLTK word tokenization
nltk_words = word_tokenize(text)
print("NLTK tokens:", nltk_words)
print(f"Count: {len(nltk_words)}")

 NLTK sentence tokenization
sentences = sent_tokenize(text)
print("\nSentences:")
for i, sent in enumerate(sentences, 1):
    print(f"{i}. {sent}")

 spaCy tokenization
nlp = spacy.load('en_core_web_sm')
doc = nlp(text)
spacy_words = [token.text for token in doc]
print("\nspaCy tokens:", spacy_words)

 Compare tokenizers
print("\n--- Tokenization Comparison ---")
print(f"NLTK: {len(nltk_words)} tokens")
print(f"spaCy: {len(spacy_words)} tokens")

 Custom tokenizer for social media
def social_media_tokenize(text):
     Keep hashtags, mentions, emojis as single tokens
    pattern = r'(\w+|@\w+|:\w+:|\S+)'
    return re.findall(pattern, text)

social_text = "Love this! NLP @OpenAI 😊 Check it out: https://example.com"
print("\nSocial media tokens:", social_media_tokenize(social_text))
```

Practice:
- [ ] Compare 5 different tokenizers on same text
- [ ] Implement custom tokenizer for specific domain
- [ ] Handle contractions (don't → do + n't)
- [ ] Tokenize multilingual text

---

 Day 3: Normalization & Text Cleaning
Lecture: [04_normalization.md](./lecture_notes/04_normalization.md)

Topics:
- Stopword removal (NLTK, spaCy, custom lists)
- Stemming (Porter, Snowball, Lancaster)
- Lemmatization (WordNet, spaCy)
- Stemming vs Lemmatization trade-offs
- Handling negations and contractions

Hands-On:
```python
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer, WordNetLemmatizer
from nltk import pos_tag
import nltk

nltk.download('stopwords')
nltk.download('wordnet')
nltk.download('averaged_perceptron_tagger')
nltk.download('averaged_perceptron_tagger_eng')

 Sample text
text = "The quick brown foxes are jumping over the lazy dogs. They aren't playing."
tokens = word_tokenize(text.lower())

 Stopword removal
stop_words = set(stopwords.words('english'))
filtered_tokens = [w for w in tokens if w not in stop_words and w.isalpha()]
print("Without stopwords:", filtered_tokens)

 Stemming
stemmer = PorterStemmer()
stemmed = [stemmer.stem(w) for w in filtered_tokens]
print("\nStemmed:", stemmed)

 Lemmatization
lemmatizer = WordNetLemmatizer()
lemmatized = [lemmatizer.lemmatize(w) for w in filtered_tokens]
print("Lemmatized:", lemmatized)

 spaCy lemmatization with POS
doc = nlp(text)
print("\nspaCy lemmas with POS:")
for token in doc:
    if not token.is_stop and token.is_alpha:
        print(f"{token.text:12} → {token.lemma_:12} ({token.pos_})")

 Compare stemming vs lemmatization
comparison_words = ['running', 'flies', 'better', 'studies', 'went']
print("\n--- Comparison ---")
print(f"{'Word':<12} {'Stem':<12} {'Lemma':<12}")
print("-"  36)
for word in comparison_words:
    stem = stemmer.stem(word)
    lemma = lemmatizer.lemmatize(word)
    print(f"{word:<12} {stem:<12} {lemma:<12}")
```

Practice:
- [ ] Compare stemming vs lemmatization on 100 words
- [ ] Build custom stopword list for domain
- [ ] Handle negations (not good vs good)
- [ ] Implement lemmatization with POS tagging

---

 Day 4: Regular Expressions for NLP
Lecture: [05_regex_for_nlp.md](./lecture_notes/05_regex_for_nlp.md)

Topics:
- Regex patterns for text extraction
- Email, phone, URL extraction
- Date and time patterns
- Named entity patterns
- Regex for text cleaning

Hands-On:
```python
import re

text = """
Contact us at support@company.com or sales@company.co.uk
Call: +1-555-123-4567 or (555) 987-6543
Visit: https://company.com or www.example.org/path?param=1
Meeting: 2024-01-15 at 14:30 or Jan 15, 2024 2:30 PM
Price: $1,234.56 or €999,99 or £500
ID: ABC123, Ref: 2024/001
"""

 Email extraction
email_pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
emails = re.findall(email_pattern, text)
print("Emails:", emails)

 Phone extraction
phone_pattern = r'[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4}'
phones = re.findall(phone_pattern, text)
print("Phones:", phones)

 URL extraction
url_pattern = r'https?://\S+|www\.\S+'
urls = re.findall(url_pattern, text)
print("URLs:", urls)

 Date extraction (multiple formats)
date_patterns = [
    r'\d{4}-\d{2}-\d{2}',   2024-01-15
    r'[A-Za-z]{3}\s+\d{1,2},?\s+\d{4}',   Jan 15, 2024
]
dates = []
for pattern in date_patterns:
    dates.extend(re.findall(pattern, text))
print("Dates:", dates)

 Currency extraction
currency_pattern = r'[$€£]\d{1,3}(?:,\d{3})(?:\.\d{2})?'
currencies = re.findall(currency_pattern, text)
print("Currencies:", currencies)

 Extract hashtags and mentions (social media)
social_text = "Great talk by @speaker at NLP2024! MachineLearning AI"
hashtags = re.findall(r'\w+', social_text)
mentions = re.findall(r'@\w+', social_text)
print("\nSocial Media:")
print("Hashtags:", hashtags)
print("Mentions:", mentions)

 Advanced: Extract text between markers
def extract_between(text, start, end):
    pattern = f'{re.escape(start)}(.?){re.escape(end)}'
    return re.findall(pattern, text, re.DOTALL)

html = "<div>Content 1</div><div>Content 2</div>"
contents = extract_between(html, '<div>', '</div>')
print("\nExtracted from HTML:", contents)
```

Practice:
- [ ] Build regex for extracting all entity types from documents
- [ ] Create log parser with regex
- [ ] Implement text redaction system
- [ ] Build pattern matching for domain-specific terms

---

 Day 5: Feature Extraction - BoW & TF-IDF
Lecture: [06_feature_extraction.md](./lecture_notes/06_feature_extraction.md)

Topics:
- Bag of Words (BoW) representation
- TF-IDF (Term Frequency - Inverse Document Frequency)
- N-grams (unigrams, bigrams, trigrams)
- CountVectorizer and TfidfVectorizer
- Feature matrix interpretation

Hands-On:
```python
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
import pandas as pd

 Sample documents
documents = [
    "Natural language processing is fascinating",
    "Machine learning enables language understanding",
    "Deep learning improves NLP applications",
    "Language models are powerful tools"
]

 Bag of Words
bow_vectorizer = CountVectorizer()
bow_matrix = bow_vectorizer.fit_transform(documents)

print("Bag of Words:")
print(f"Shape: {bow_matrix.shape}")
print(f"Vocabulary: {list(bow_vectorizer.vocabulary_.keys())[:10]}...")
print("\nFeature matrix:")
print(bow_matrix.toarray())

 TF-IDF
tfidf_vectorizer = TfidfVectorizer()
tfidf_matrix = tfidf_vectorizer.fit_transform(documents)

print("\nTF-IDF:")
print(f"Shape: {tfidf_matrix.shape}")

 Get feature names
feature_names = tfidf_vectorizer.get_feature_names_out()

 Create DataFrame for better visualization
df_tfidf = pd.DataFrame(
    tfidf_matrix.toarray(),
    columns=feature_names,
    index=[f"Doc {i+1}" for i in range(len(documents))]
)

print("\nTF-IDF scores (first 5 features):")
print(df_tfidf.iloc[:, :5].round(3))

 Find most important words per document
print("\nTop words per document:")
for i, doc in enumerate(documents):
    tfidf_scores = tfidf_matrix[i].toarray()[0]
    top_indices = tfidf_scores.argsort()[-3:][::-1]
    top_words = [(feature_names[j], tfidf_scores[j]) for j in top_indices]
    print(f"Doc {i+1}: {top_words}")

 N-grams (bigrams and trigrams)
ngram_vectorizer = TfidfVectorizer(ngram_range=(1, 2))   unigrams + bigrams
ngram_matrix = ngram_vectorizer.fit_transform(documents)
print(f"\nN-grams (1-2): {ngram_matrix.shape}")
print(f"Features: {len(ngram_vectorizer.get_feature_names_out())}")

 Custom preprocessing pipeline
def custom_preprocess(text):
     Lowercase
    text = text.lower()
     Tokenize
    tokens = word_tokenize(text)
     Remove stopwords and non-alpha
    tokens = [t for t in tokens if t.isalpha() and t not in stop_words]
     Lemmatize
    tokens = [lemmatizer.lemmatize(t) for t in tokens]
    return ' '.join(tokens)

 Apply preprocessing
processed_docs = [custom_preprocess(doc) for doc in documents]
print("\nProcessed documents:")
for i, doc in enumerate(processed_docs):
    print(f"{i+1}. {doc}")

 Create final feature matrix
final_vectorizer = TfidfVectorizer(max_features=100, min_df=1)
final_matrix = final_vectorizer.fit_transform(processed_docs)
print(f"\nFinal feature matrix: {final_matrix.shape}")
```

Practice:
- [ ] Compare BoW vs TF-IDF on classification task
- [ ] Experiment with different n-gram ranges
- [ ] Implement custom vectorizer
- [ ] Build feature importance analyzer

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_nlp_introduction.md](./lecture_notes/01_nlp_introduction.md) - NLP fundamentals
   - [02_text_preprocessing.md](./lecture_notes/02_text_preprocessing.md) - Cleaning techniques
   - [03_tokenization.md](./lecture_notes/03_tokenization.md) - Tokenization methods
   - [04_normalization.md](./lecture_notes/04_normalization.md) - Stemming & lemmatization
   - [05_regex_for_nlp.md](./lecture_notes/05_regex_for_nlp.md) - Pattern matching
   - [06_feature_extraction.md](./lecture_notes/06_feature_extraction.md) - Text to vectors

2. External Resources:
   - [NLTK Book](https://www.nltk.org/book/) - Comprehensive NLP guide
   - [spaCy Documentation](https://spacy.io/usage) - Industrial-strength NLP
   - [HuggingFace Tokenizers](https://huggingface.co/docs/tokenizers/) - Fast tokenization
   - [Scikit-learn Text](https://scikit-learn.org/stable/modules/feature_extraction.htmltext-feature-extraction) - Feature extraction

 Tools & Setup
```bash
 Install required packages
pip install nltk spacy scikit-learn pandas numpy regex

 Download NLTK data
python -c "import nltk; nltk.download('punkt'); nltk.download('stopwords'); nltk.download('wordnet'); nltk.download('averaged_perceptron_tagger')"

 Download spaCy model
python -m spacy download en_core_web_sm

 Optional: Additional spaCy models
 python -m spacy download en_core_web_md
 python -m spacy download en_core_web_lg
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Text Preprocessing Pipeline (30%)
   - Modular preprocessing functions
   - HTML/URL/email cleaning
   - Handles multiple text formats
   - Documented with examples

2. Tokenization Comparison (20%)
   - Comparison of 4+ tokenizers
   - Performance benchmarking
   - Domain-specific tokenizer
   - Analysis report

3. Normalization Demo (20%)
   - Stemming vs lemmatization comparison
   - Stopword handling strategies
   - POS-aware lemmatization
   - Visual comparison

4. Regex Extraction Tool (15%)
   - Extract emails, phones, dates, URLs
   - Custom entity patterns
   - Text redaction capability
   - Pattern library

5. Feature Extraction Suite (15%)
   - BoW implementation
   - TF-IDF with analysis
   - N-gram exploration
   - Feature visualization

---

 🎯 Exercises

 Exercise 1: Social Media Text Cleaner
Create a preprocessing pipeline specifically for social media:
- Handle emojis and emoticons
- Process hashtags and mentions
- Clean URLs and media links
- Normalize slang and abbreviations
- Preserve sentiment indicators

 Exercise 2: Multilingual Tokenizer
Build a tokenizer that handles:
- English, French, Spanish text
- Language detection
- Script-specific rules
- Unicode normalization

 Exercise 3: Information Extractor
Create a system that extracts:
- Named entities (person, org, location)
- Dates and times
- Monetary values
- Contact information
- Document metadata

 Exercise 4: Feature Engineering
Implement advanced text features:
- Character n-grams
- Word shape features
- POS tag ratios
- Sentence length statistics
- Readability scores

---

 📋 Assignment: Complete NLP Preprocessing Pipeline

 Part A: Text Cleaning (25 points)
- [ ] Handle HTML, XML, markdown (5 pts)
- [ ] Remove noise (URLs, emails, special chars) (5 pts)
- [ ] Normalize encoding and whitespace (5 pts)
- [ ] Handle emojis and special Unicode (5 pts)
- [ ] Document cleaning decisions (5 pts)

 Part B: Tokenization (20 points)
- [ ] Implement 3+ tokenization strategies (7 pts)
- [ ] Handle contractions and punctuation (5 pts)
- [ ] Sentence segmentation (4 pts)
- [ ] Performance comparison (4 pts)

 Part C: Normalization (20 points)
- [ ] Stopword removal (customizable) (5 pts)
- [ ] Stemming implementation (5 pts)
- [ ] Lemmatization with POS (7 pts)
- [ ] Handling of edge cases (3 pts)

 Part D: Feature Extraction (20 points)
- [ ] BoW implementation (5 pts)
- [ ] TF-IDF with analysis (7 pts)
- [ ] N-gram features (5 pts)
- [ ] Feature selection (3 pts)

 Part E: Integration & Testing (15 points)
- [ ] Complete pipeline class/function (7 pts)
- [ ] Unit tests for all components (5 pts)
- [ ] Documentation and examples (3 pts)

---

 � Quick Navigation

| Resource | Description |
|----------|-------------|
| [📖 Lecture Notes](./lecture_notes/) | NLP concepts and techniques |
| [🎯 Exercises](./exercises/) | Hands-on practice |
| [📋 Assignments](./assignments/) | Graded deliverables |
| [📄 Cheatsheets](./cheatsheets/) | Quick reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Read lecture notes |
| 45 min | Code implementation |
| 15 min | Experiment with different approaches |
| 15 min | Document and commit |

Total Daily Time: ~1.75 hours

---

 💡 Success Tips for Week 1

1. Experiment with Real Data: Use actual tweets, news articles, emails
2. Compare Approaches: Don't settle on first solution
3. Handle Edge Cases: Test with messy, real-world text
4. Visualize Results: See what your code produces
5. Time Your Code: Performance matters for large datasets
6. Document Decisions: Why you chose specific tokenizer/normalizer
7. Build Reusable Components: Create functions/classes
8. Test on Multiple Languages: Even if just basics

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `nltk.download()` fails | Check internet, use proxy if needed |
| `spacy.load()` error | Run `python -m spacy download en_core_web_sm` |
| `MemoryError` with large texts | Use generators, process in batches |
| `UnicodeDecodeError` | Specify encoding: `open(file, 'r', encoding='utf-8')` |
| `TfidfVectorizer` too slow | Use `max_features`, `min_df`, `max_df` limits |
| Tokenization inconsistencies | Test with edge cases (contractions, hyphens) |

---

 🎓 Week 1 Completion Checklist

- [ ] NLTK and spaCy installed
- [ ] Text cleaner handles HTML/URLs/emails
- [ ] Compared multiple tokenizers
- [ ] Implemented stemming and lemmatization
- [ ] Regex patterns for common entities
- [ ] BoW and TF-IDF working
- [ ] Pipeline class/function created
- [ ] Unit tests passing
- [ ] Documentation complete
- [ ] All exercises done
- [ ] Assignment submitted

---

Previous: [Month 2: ML Techniques ←](../../Month_2_ML_Techniques/README.md) | Next: [Week 2: Word Embeddings →](../Week_2/README.md)
