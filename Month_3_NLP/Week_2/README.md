 Week 2: Word Embeddings & Advanced NLP

Duration: 5 days | Theme: Semantic Understanding of Text | Difficulty: Intermediate  
Prerequisite: Week 1 (NLP Fundamentals)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Build and train Word2Vec models (CBOW and Skip-gram)
- ✅ Understand and use pre-trained embeddings (GloVe, FastText)
- ✅ Perform word similarity and analogy tasks
- ✅ Implement Named Entity Recognition (NER) systems
- ✅ Explore transformer architectures (BERT basics)
- ✅ Apply semantic understanding to real-world tasks

---

 📖 Detailed Daily Schedule

 Day 1: Word Embeddings Fundamentals
Lecture: [01_word_embeddings_intro.md](./lecture_notes/01_word_embeddings_intro.md) | [02_word2vec.md](./lecture_notes/02_word2vec.md)

Topics:
- One-hot encoding limitations
- Dense vector representations
- Semantic similarity concepts
- Word2Vec architecture: CBOW vs Skip-gram
- Training objective and negative sampling

Hands-On:
```python
import gensim
from gensim.models import Word2Vec
import numpy as np

 Sample corpus (tokenized sentences)
sentences = [
    ['natural', 'language', 'processing', 'is', 'fascinating'],
    ['machine', 'learning', 'enables', 'language', 'understanding'],
    ['deep', 'learning', 'improves', 'nlp', 'applications'],
    ['word', 'embeddings', 'capture', 'semantic', 'meaning'],
    ['neural', 'networks', 'learn', 'from', 'data'],
    ['artificial', 'intelligence', 'transforms', 'industries'],
    ['data', 'science', 'uses', 'machine', 'learning'],
    ['python', 'is', 'great', 'for', 'nlp'],
    ['text', 'mining', 'extracts', 'valuable', 'insights'],
    ['sentiment', 'analysis', 'classifies', 'opinions'],
]

 Train Word2Vec model (Skip-gram)
model = Word2Vec(
    sentences=sentences,
    vector_size=50,       Embedding dimension
    window=3,             Context window size
    min_count=1,          Minimum word frequency
    workers=4,            Number of worker threads
    sg=1,                 1=Skip-gram, 0=CBOW
    epochs=100            Training epochs
)

 Get word vector
vector = model.wv['language']
print(f"'language' vector shape: {vector.shape}")
print(f"First 10 values: {vector[:10]}")

 Find similar words
similar = model.wv.most_similar('learning', topn=5)
print(f"\nWords similar to 'learning':")
for word, score in similar:
    print(f"  {word}: {score:.4f}")

 Calculate similarity between words
sim = model.wv.similarity('machine', 'learning')
print(f"\nSimilarity (machine, learning): {sim:.4f}")

 Save and load model
model.save("word2vec.model")
loaded_model = Word2Vec.load("word2vec.model")
```

Practice:
- [ ] Train Word2Vec on larger corpus (Wikipedia sample)
- [ ] Compare CBOW vs Skip-gram performance
- [ ] Visualize embeddings with t-SNE
- [ ] Tune hyperparameters (vector_size, window, min_count)

---

 Day 2: Pre-trained Embeddings & GloVe
Lecture: [03_glove_and_pretrained.md](./lecture_notes/03_glove_and_pretrained.md)

Topics:
- GloVe (Global Vectors) methodology
- Pre-trained embeddings advantages
- Loading GloVe embeddings
- FastText for subword information
- Handling out-of-vocabulary (OOV) words

Hands-On:
```python
import numpy as np
from gensim.models import KeyedVectors

 Load pre-trained GloVe (you need to download first)
 Download from: https://nlp.stanford.edu/projects/glove/
 glove_file = 'glove.6B.50d.txt'

 Convert GloVe to Word2Vec format
def load_glove_embeddings(glove_file):
    embeddings = {}
    with open(glove_file, 'r', encoding='utf-8') as f:
        for line in f:
            values = line.split()
            word = values[0]
            vector = np.asarray(values[1:], dtype='float32')
            embeddings[word] = vector
    return embeddings

 Example with small embedding dict
embeddings = {
    'king': np.array([0.2, 0.3, 0.4, 0.1]),
    'queen': np.array([0.2, 0.3, 0.5, 0.2]),
    'man': np.array([0.1, 0.2, 0.3, 0.1]),
    'woman': np.array([0.1, 0.2, 0.4, 0.2]),
}

def cosine_similarity(v1, v2):
    return np.dot(v1, v2) / (np.linalg.norm(v1)  np.linalg.norm(v2))

 Word analogy: king - man + woman = queen
result = embeddings['king'] - embeddings['man'] + embeddings['woman']
print(f"Analogy result: {result}")

 Find closest word (in real scenario, search all embeddings)
for word, vec in embeddings.items():
    sim = cosine_similarity(result, vec)
    print(f"Similarity to '{word}': {sim:.4f}")

 Using gensim to load pre-trained
 model = KeyedVectors.load_word2vec_format('glove.6B.50d.w2v.txt', binary=False)

 FastText for OOV words
from gensim.models import FastText

 Train FastText (handles subwords)
fasttext_model = FastText(
    sentences=sentences,
    vector_size=50,
    window=3,
    min_count=1,
    epochs=100,
    min_n=3,     Min character n-gram
    max_n=6      Max character n-gram
)

 FastText can handle OOV words using subwords
oov_vector = fasttext_model.wv['unseenword']
print(f"OOV word vector: {oov_vector[:5]}")
```

Practice:
- [ ] Load and explore GloVe embeddings
- [ ] Implement word analogy solver
- [ ] Compare Word2Vec vs FastText on OOV words
- [ ] Build semantic search using embeddings

---

 Day 3: Named Entity Recognition (NER)
Lecture: [04_named_entity_recognition.md](./lecture_notes/04_named_entity_recognition.md)

Topics:
- What is NER? Types of entities
- Rule-based vs ML vs Deep Learning approaches
- spaCy NER pipeline
- Training custom NER models
- Evaluating NER performance

Hands-On:
```python
import spacy
from spacy import displacy

 Load English model
nlp = spacy.load('en_core_web_sm')

 Sample text
text = """
Apple Inc. is planning to open a new store in London next year. 
Tim Cook announced the expansion on January 15, 2024.
The company will invest $5 million in the project.
"""

 Process text
doc = nlp(text)

 Extract entities
print("Named Entities:")
for ent in doc.ents:
    print(f"  {ent.text:<25} | {ent.label_:<15} | {spacy.explain(ent.label_)}")

 Visualize
print("\nVisualization:")
displacy.render(doc, style='ent', jupyter=True)

 Entity statistics
from collections import Counter
entities = [(ent.text, ent.label_) for ent in doc.ents]
print(f"\nEntity counts: {Counter(entities)}")

 Custom NER training (simplified example)
TRAIN_DATA = [
    ("I love my iPhone 15", {"entities": [(10, 19, "PRODUCT")]}),
    ("Google released new AI features", {"entities": [(0, 6, "ORG")]}),
    ("Microsoft is based in Seattle", {"entities": [(0, 9, "ORG"), (24, 31, "GPE")]}),
]

 Create blank model
nlp_blank = spacy.blank('en')
ner = nlp_blank.add_pipe('ner')

 Add labels
for _, annotations in TRAIN_DATA:
    for ent in annotations.get('entities'):
        ner.add_label(ent[2])

 Training (simplified - normally needs more iterations)
optimizer = nlp_blank.begin_training()
for i in range(100):
    for text, annotations in TRAIN_DATA:
        nlp_blank.update([text], [annotations], sgd=optimizer)

 Test custom model
doc_test = nlp_blank("I bought an iPhone 15 yesterday")
print("\nCustom NER results:")
for ent in doc_test.ents:
    print(f"  {ent.text} - {ent.label_}")
```

Practice:
- [ ] Extract entities from news articles
- [ ] Build entity linking system
- [ ] Train custom NER on domain data
- [ ] Evaluate NER with precision/recall/F1

---

 Day 4: Transformers & BERT Introduction
Lecture: [05_transformers_intro.md](./lecture_notes/05_transformers_intro.md) | [06_bert_basics.md](./lecture_notes/06_bert_basics.md)

Topics:
- RNN/LSTM limitations
- Attention mechanism intuition
- Transformer architecture overview
- Self-attention explained
- BERT: Bidirectional Encoder Representations
- Using pre-trained BERT models

Hands-On:
```python
from transformers import (
    BertTokenizer, 
    BertModel, 
    pipeline,
    AutoTokenizer,
    AutoModel
)
import torch

 Load pre-trained BERT tokenizer and model
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained('bert-base-uncased')

 Tokenize text
text = "Natural language processing is amazing!"
tokens = tokenizer.tokenize(text)
print(f"Tokens: {tokens}")

 Encode for model
inputs = tokenizer(text, return_tensors='pt', padding=True, truncation=True)
print(f"Input IDs: {inputs['input_ids']}")
print(f"Attention mask: {inputs['attention_mask']}")

 Get embeddings (contextualized!)
with torch.no_grad():
    outputs = model(inputs)
    embeddings = outputs.last_hidden_state

print(f"\nEmbedding shape: {embeddings.shape}")
print(f"  (batch_size, sequence_length, hidden_size)")

 Using HuggingFace pipelines (easier interface)
 Sentiment Analysis
classifier = pipeline('sentiment-analysis')
result = classifier("I love working with NLP!")
print(f"\nSentiment: {result}")

 Named Entity Recognition
ner_pipeline = pipeline('ner', aggregation_strategy='simple')
text = "Hugging Face is a company based in New York City."
entities = ner_pipeline(text)
print(f"\nNER: {entities}")

 Feature Extraction
feature_extractor = pipeline('feature-extraction')
features = feature_extractor("Hello world", return_tensors=True)
print(f"\nFeatures shape: {features.shape}")

 Question Answering
qa_pipeline = pipeline('question-answering')
context = "BERT was created by Google AI Language researchers."
question = "Who created BERT?"
answer = qa_pipeline(question=question, context=context)
print(f"\nQA Answer: {answer}")

 Using Auto classes (works with any model)
tokenizer = AutoTokenizer.from_pretrained('distilbert-base-uncased')
model = AutoModel.from_pretrained('distilbert-base-uncased')
```

Practice:
- [ ] Compare BERT embeddings vs Word2Vec
- [ ] Fine-tune BERT for classification task
- [ ] Extract features for downstream ML
- [ ] Explore different transformer models (RoBERTa, DistilBERT)

---

 Day 5: Semantic Applications & Project
Lecture: [07_semantic_applications.md](./lecture_notes/07_semantic_applications.md)

Complete Project: Semantic Document Analyzer

```python
 Complete Semantic Analysis System
import gensim
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity
from transformers import pipeline
import spacy

class SemanticAnalyzer:
    def __init__(self):
        self.nlp = spacy.load('en_core_web_sm')
        self.sentiment = pipeline('sentiment-analysis')
        self.ner = pipeline('ner', aggregation_strategy='simple')
        
    def analyze_text(self, text):
        """Complete semantic analysis pipeline"""
        results = {
            'entities': self.extract_entities(text),
            'sentiment': self.analyze_sentiment(text),
            'key_phrases': self.extract_key_phrases(text),
            'summary': self.generate_summary(text)
        }
        return results
    
    def extract_entities(self, text):
        doc = self.nlp(text)
        entities = {}
        for ent in doc.ents:
            if ent.label_ not in entities:
                entities[ent.label_] = []
            entities[ent.label_].append(ent.text)
        return entities
    
    def analyze_sentiment(self, text):
        result = self.sentiment(text[:512])[0]   Truncate for model limit
        return {
            'label': result['label'],
            'confidence': result['score']
        }
    
    def extract_key_phrases(self, text):
        doc = self.nlp(text)
        phrases = []
        for chunk in doc.noun_chunks:
            if len(chunk.text.split()) > 1:
                phrases.append(chunk.text)
        return list(set(phrases))[:10]   Top 10 unique phrases
    
    def generate_summary(self, text, num_sentences=3):
        doc = self.nlp(text)
        sentences = [sent.text for sent in doc.sents]
        
         Simple extractive summary (first N sentences)
         In production, use more sophisticated methods
        return ' '.join(sentences[:num_sentences])
    
    def semantic_similarity(self, text1, text2):
        """Calculate semantic similarity using embeddings"""
        doc1 = self.nlp(text1)
        doc2 = self.nlp(text2)
        
         Use spaCy's built-in similarity
        return doc1.similarity(doc2)

 Usage example
analyzer = SemanticAnalyzer()

document = """
Apple Inc. reported record quarterly revenue of $123.9 billion.
CEO Tim Cook highlighted strong iPhone 15 sales and services growth.
The company's stock rose 5% following the earnings announcement.
Analysts praised Apple's continued innovation in AI and machine learning.
"""

results = analyzer.analyze_text(document)
print("Semantic Analysis Results:")
print(f"Entities: {results['entities']}")
print(f"Sentiment: {results['sentiment']}")
print(f"Key Phrases: {results['key_phrases']}")
print(f"Summary: {results['summary']}")

 Compare two documents
doc2 = "Microsoft announced strong cloud revenue growth."
similarity = analyzer.semantic_similarity(document, doc2)
print(f"\nSemantic Similarity: {similarity:.4f}")
```

Practice:
- [ ] Build semantic search engine
- [ ] Create document clustering system
- [ ] Implement question-answering bot
- [ ] Develop text recommendation engine

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_word_embeddings_intro.md](./lecture_notes/01_word_embeddings_intro.md) - Embedding fundamentals
   - [02_word2vec.md](./lecture_notes/02_word2vec.md) - Word2Vec architecture
   - [03_glove_and_pretrained.md](./lecture_notes/03_glove_and_pretrained.md) - Pre-trained models
   - [04_named_entity_recognition.md](./lecture_notes/04_named_entity_recognition.md) - NER systems
   - [05_transformers_intro.md](./lecture_notes/05_transformers_intro.md) - Attention mechanism
   - [06_bert_basics.md](./lecture_notes/06_bert_basics.md) - BERT usage
   - [07_semantic_applications.md](./lecture_notes/07_semantic_applications.md) - Practical applications

2. External Resources:
   - [Word2Vec Paper](https://arxiv.org/abs/1301.3781) - Original paper
   - [GloVe Website](https://nlp.stanford.edu/projects/glove/) - Pre-trained vectors
   - [Transformers Library](https://huggingface.co/docs/transformers/) - HuggingFace docs
   - [BERT Paper](https://arxiv.org/abs/1810.04805) - Original BERT

 Tools & Setup
```bash
 Install required packages
pip install gensim spacy transformers torch tensorflow

 Download spaCy models
python -m spacy download en_core_web_sm
python -m spacy download en_core_web_md   Larger model

 Optional: Download pre-trained embeddings
 GloVe: https://nlp.stanford.edu/projects/glove/
 Word2Vec: https://code.google.com/archive/p/word2vec/
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Word Embeddings Implementation (25%)
   - Trained Word2Vec model
   - Similarity analysis report
   - Visualization of embeddings
   - Hyperparameter comparison

2. NER System (25%)
   - Custom NER model or pipeline
   - Evaluation on test data
   - Entity statistics and analysis
   - Performance metrics

3. BERT Applications (25%)
   - Fine-tuned BERT for task
   - Feature extraction pipeline
   - Comparison with embeddings
   - Inference optimization

4. Semantic Project (25%)
   - Complete semantic analyzer
   - Documentation and examples
   - Performance evaluation
   - Real-world application demo

---

 🎯 Exercises

 Exercise 1: Word Embedding Explorer
Build interactive tool to:
- Visualize word relationships
- Solve analogies automatically
- Find closest words in embedding space
- Compare different embedding models

 Exercise 2: NER Evaluation Framework
Create comprehensive NER evaluation:
- Precision, recall, F1 calculation
- Entity-level and token-level metrics
- Error analysis and visualization
- Cross-model comparison

 Exercise 3: Transformer Feature Extraction
Implement efficient feature extraction:
- Batch processing for large datasets
- Different layer selection strategies
- Pooling strategies (CLS, mean, max)
- Dimensionality reduction

 Exercise 4: Semantic Search Engine
Build search system using:
- Document embeddings
- Query understanding
- Relevance ranking
- Real-time indexing

---

 📋 Assignment: Semantic Understanding System

 Part A: Word Embeddings (25 points)
- [ ] Train Word2Vec on custom corpus (7 pts)
- [ ] Load and use pre-trained embeddings (6 pts)
- [ ] Implement word analogies (6 pts)
- [ ] Visualize embedding space (6 pts)

 Part B: Named Entity Recognition (25 points)
- [ ] Extract entities from documents (8 pts)
- [ ] Train/fine-tune NER model (10 pts)
- [ ] Evaluate performance metrics (7 pts)

 Part C: Transformer Models (25 points)
- [ ] Use pre-trained BERT/RoBERTa (10 pts)
- [ ] Fine-tune for specific task (10 pts)
- [ ] Compare with traditional methods (5 pts)

 Part D: Integration Project (25 points)
- [ ] Complete semantic pipeline (10 pts)
- [ ] Real-world application (10 pts)
- [ ] Documentation and testing (5 pts)

---

 � Quick Navigation

| Resource | Description |
|----------|-------------|
| [📖 Lecture Notes](./lecture_notes/) | Embeddings, NER, Transformers |
| [🎯 Exercises](./exercises/) | Hands-on practice |
| [📋 Assignments](./assignments/) | Graded deliverables |
| [📄 Cheatsheets](./cheatsheets/) | Quick API reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Read lecture notes |
| 45 min | Implement models |
| 15 min | Experiment and tune |
| 15 min | Document results |

Total Daily Time: ~1.75 hours

---

 💡 Success Tips for Week 2

1. Start Small: Use toy examples before large models
2. GPU Recommended: Transformers are compute-intensive
3. Use Pre-trained: Don't train from scratch initially
4. Compare Models: Benchmark different approaches
5. Monitor Memory: Large models need resources
6. Batch Processing: Process documents efficiently
7. Cache Results: Save embeddings to avoid recomputation
8. Read Papers: Understand theory behind implementations

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `CUDA out of memory` | Reduce batch size, use CPU, or smaller model |
| `Word2Vec training slow` | Increase workers, use skip-gram for large vocab |
| `BERT tokens > 512` | Truncate or split long documents |
| `NER poor performance` | More training data, better labels, or fine-tune |
| `Gensim load error` | Check file format, use correct loading method |
| `Transformer download fails` | Check internet, use local cache |

---

 🎓 Week 2 Completion Checklist

- [ ] Word2Vec model trained
- [ ] Pre-trained embeddings loaded
- [ ] Word analogies implemented
- [ ] NER system working
- [ ] BERT model fine-tuned/used
- [ ] Semantic analyzer built
- [ ] All evaluations complete
- [ ] Documentation done
- [ ] Exercises submitted
- [ ] Assignment delivered

---

Previous: [Week 1: NLP Fundamentals ←](../Week_1/README.md) | Next: [Week 3: Sequence Models →](../Week_3/README.md)
