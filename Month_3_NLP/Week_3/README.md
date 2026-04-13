 Week 3: Sentiment Analysis & Text Classification

Duration: 5 days | Theme: Practical NLP Applications | Difficulty: Intermediate  
Prerequisite: Weeks 1-2 (NLP Fundamentals, Word Embeddings)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Build sentiment analysis systems from scratch
- ✅ Create robust text classification pipelines
- ✅ Implement multi-class and multi-label classification
- ✅ Apply transfer learning with BERT/RoBERTa
- ✅ Deploy text models as APIs
- ✅ Evaluate and optimize classifier performance

---

 📖 Detailed Daily Schedule

 Day 1: Sentiment Analysis Fundamentals
Lecture: [01_sentiment_analysis_basics.md](./lecture_notes/01_sentiment_analysis_basics.md)

Topics:
- What is sentiment analysis? Applications
- Polarity detection (positive, negative, neutral)
- Rule-based vs ML vs Deep Learning approaches
- Lexicon-based methods (VADER, TextBlob)
- Dataset preparation and labeling

Hands-On:
```python
from textblob import TextBlob
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import pandas as pd

 Sample reviews
reviews = [
    "This product is absolutely amazing! Best purchase ever.",
    "Terrible quality, complete waste of money.",
    "It's okay, nothing special but does the job.",
    "Love the design, but battery life could be better.",
    "Would not recommend. Broke after one week.",
]

 TextBlob approach
print("=== TextBlob Results ===")
for review in reviews:
    blob = TextBlob(review)
    polarity = blob.sentiment.polarity
    subjectivity = blob.sentiment.subjectivity
    sentiment = "Positive" if polarity > 0 else "Negative" if polarity < 0 else "Neutral"
    print(f"{sentiment:<10} | Polarity: {polarity:>6.3f} | {review[:50]}...")

 VADER approach (better for social media, handles emojis)
analyzer = SentimentIntensityAnalyzer()
print("\n=== VADER Results ===")
for review in reviews:
    scores = analyzer.polarity_scores(review)
    compound = scores['compound']
    sentiment = "Positive" if compound >= 0.05 else "Negative" if compound <= -0.05 else "Neutral"
    print(f"{sentiment:<10} | Compound: {compound:>6.3f} | {review[:50]}...")

 Create labeled dataset for ML approach
data = {
    'text': reviews,
    'sentiment': [1, 0, 0, 1, 0],   1=Positive, 0=Negative
    'polarity': [0.9, -0.8, 0.1, 0.6, -0.9]
}
df = pd.DataFrame(data)
print("\n=== Dataset Sample ===")
print(df.head())

 Simple rule-based classifier
def rule_based_sentiment(text):
    positive_words = ['amazing', 'love', 'best', 'great', 'excellent', 'good', 'perfect']
    negative_words = ['terrible', 'waste', 'bad', 'awful', 'hate', 'worst', 'broke']
    
    text_lower = text.lower()
    pos_count = sum(1 for word in positive_words if word in text_lower)
    neg_count = sum(1 for word in negative_words if word in text_lower)
    
    if pos_count > neg_count:
        return "Positive"
    elif neg_count > pos_count:
        return "Negative"
    else:
        return "Neutral"

print("\n=== Rule-Based Results ===")
for review in reviews:
    sentiment = rule_based_sentiment(review)
    print(f"{sentiment:<10} | {review[:50]}...")
```

Practice:
- [ ] Build lexicon-based sentiment analyzer
- [ ] Compare VADER vs TextBlob on tweets
- [ ] Create custom sentiment dictionary
- [ ] Handle negation in sentiment ("not good")

---

 Day 2: Machine Learning for Text Classification
Lecture: [02_ml_text_classification.md](./lecture_notes/02_ml_text_classification.md)

Topics:
- Traditional ML approaches (Naive Bayes, SVM, Logistic Regression)
- Feature extraction for classification
- TF-IDF + ML pipeline
- Cross-validation and hyperparameter tuning
- Model evaluation metrics

Hands-On:
```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.linear_model import LogisticRegression
from sklearn.svm import LinearSVC
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.metrics import classification_report, confusion_matrix, accuracy_score
import numpy as np

 Sample dataset (expand with real data)
texts = [
    "I love this product, it's amazing",
    "Best purchase I've ever made",
    "Excellent quality and fast shipping",
    "Terrible experience, product broke",
    "Waste of money, do not buy",
    "Awful customer service",
    "It's okay, average quality",
    "Not bad, but expected better",
    "Decent for the price",
    "Neither good nor bad",
]  10   Repeat for more samples

labels = [1, 1, 1, 0, 0, 0, 0, 0, 0, 0]  10   1=Positive, 0=Negative

 Split data
X_train, X_test, y_train, y_test = train_test_split(
    texts, labels, test_size=0.2, random_state=42, stratify=labels
)

 TF-IDF Vectorization
vectorizer = TfidfVectorizer(
    max_features=1000,
    ngram_range=(1, 2),
    stop_words='english',
    min_df=2
)

X_train_tfidf = vectorizer.fit_transform(X_train)
X_test_tfidf = vectorizer.transform(X_test)

 Train multiple models
models = {
    'Naive Bayes': MultinomialNB(),
    'Logistic Regression': LogisticRegression(max_iter=1000),
    'Linear SVM': LinearSVC()
}

print("=== Model Comparison ===")
for name, model in models.items():
     Cross-validation
    cv_scores = cross_val_score(model, X_train_tfidf, y_train, cv=5)
    
     Train and test
    model.fit(X_train_tfidf, y_train)
    y_pred = model.predict(X_test_tfidf)
    
    accuracy = accuracy_score(y_test, y_pred)
    
    print(f"\n{name}:")
    print(f"  CV Accuracy: {cv_scores.mean():.3f} (+/- {cv_scores.std()2:.3f})")
    print(f"  Test Accuracy: {accuracy:.3f}")

 Detailed evaluation for best model
best_model = LogisticRegression(max_iter=1000)
best_model.fit(X_train_tfidf, y_train)
y_pred = best_model.predict(X_test_tfidf)

print("\n=== Detailed Classification Report ===")
print(classification_report(y_test, y_pred, target_names=['Negative', 'Positive']))

 Feature importance (coefficients)
feature_names = vectorizer.get_feature_names_out()
coef = best_model.coef_[0]
top_positive = np.argsort(coef)[-10:]
top_negative = np.argsort(coef)[:10]

print("\n=== Most Predictive Features ===")
print("Positive indicators:")
for idx in reversed(top_positive):
    print(f"  {feature_names[idx]}: {coef[idx]:.3f}")

print("\nNegative indicators:")
for idx in top_negative:
    print(f"  {feature_names[idx]}: {coef[idx]:.3f}")

 Prediction function
def predict_sentiment(text, model, vectorizer):
    text_tfidf = vectorizer.transform([text])
    prediction = model.predict(text_tfidf)[0]
    probability = model.predict_proba(text_tfidf)[0] if hasattr(model, 'predict_proba') else None
    
    sentiment = "Positive" if prediction == 1 else "Negative"
    if probability is not None:
        confidence = max(probability)
        return sentiment, confidence
    return sentiment, None

 Test predictions
test_texts = [
    "This is the best thing ever!",
    "Completely disappointed with the quality"
]

print("\n=== Sample Predictions ===")
for text in test_texts:
    sentiment, conf = predict_sentiment(text, best_model, vectorizer)
    conf_str = f" ({conf:.3f})" if conf else ""
    print(f"'{text}' -> {sentiment}{conf_str}")
```

Practice:
- [ ] Compare 5 different ML algorithms
- [ ] Tune hyperparameters with GridSearchCV
- [ ] Implement feature selection
- [ ] Create ensemble of classifiers

---

 Day 3: Deep Learning for Text Classification
Lecture: [03_dl_text_classification.md](./lecture_notes/03_dl_text_classification.md)

Topics:
- Neural network architectures for text
- CNN for text classification
- LSTM/GRU for sequence modeling
- Embedding layers
- Dropout and regularization

Hands-On:
```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, Dataset
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense, Dropout, Conv1D, GlobalMaxPooling1D
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
import tensorflow as tf

 Keras Implementation (easier for beginners)
 Prepare sequences
tokenizer = Tokenizer(num_words=5000, oov_token='<OOV>')
tokenizer.fit_on_texts(X_train)

X_train_seq = tokenizer.texts_to_sequences(X_train)
X_test_seq = tokenizer.texts_to_sequences(X_test)

max_length = 50
X_train_pad = pad_sequences(X_train_seq, maxlen=max_length, padding='post', truncating='post')
X_test_pad = pad_sequences(X_test_seq, maxlen=max_length, padding='post', truncating='post')

 LSTM Model
lstm_model = Sequential([
    Embedding(5000, 64, input_length=max_length),
    LSTM(64, dropout=0.2, recurrent_dropout=0.2),
    Dense(32, activation='relu'),
    Dropout(0.5),
    Dense(1, activation='sigmoid')
])

lstm_model.compile(
    loss='binary_crossentropy',
    optimizer='adam',
    metrics=['accuracy']
)

print("=== LSTM Model Summary ===")
lstm_model.summary()

 Train (with small data, just demo)
 history = lstm_model.fit(
     X_train_pad, np.array(y_train),
     epochs=10,
     batch_size=32,
     validation_split=0.2,
     verbose=1
 )

 CNN Model (often faster and good for text)
cnn_model = Sequential([
    Embedding(5000, 64, input_length=max_length),
    Conv1D(128, 5, activation='relu'),
    GlobalMaxPooling1D(),
    Dense(64, activation='relu'),
    Dropout(0.5),
    Dense(1, activation='sigmoid')
])

cnn_model.compile(
    loss='binary_crossentropy',
    optimizer='adam',
    metrics=['accuracy']
)

print("\n=== CNN Model Summary ===")
cnn_model.summary()

 PyTorch Implementation (more control)
class TextDataset(Dataset):
    def __init__(self, texts, labels, tokenizer, max_len):
        self.texts = texts
        self.labels = labels
        self.tokenizer = tokenizer
        self.max_len = max_len
    
    def __len__(self):
        return len(self.texts)
    
    def __getitem__(self, idx):
        text = self.texts[idx]
        label = self.labels[idx]
        
        encoding = self.tokenizer.texts_to_sequences([text])
        encoding = pad_sequences(encoding, maxlen=self.max_len, padding='post')[0]
        
        return {
            'input_ids': torch.tensor(encoding, dtype=torch.long),
            'label': torch.tensor(label, dtype=torch.float)
        }

class LSTMClassifier(nn.Module):
    def __init__(self, vocab_size, embedding_dim, hidden_dim, output_dim):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, embedding_dim)
        self.lstm = nn.LSTM(embedding_dim, hidden_dim, batch_first=True, bidirectional=True)
        self.fc = nn.Linear(hidden_dim  2, output_dim)
        self.dropout = nn.Dropout(0.5)
    
    def forward(self, text):
        embedded = self.dropout(self.embedding(text))
        lstm_out, (hidden, cell) = self.lstm(embedded)
        hidden = torch.cat((hidden[-2,:,:], hidden[-1,:,:]), dim=1)
        return self.fc(self.dropout(hidden))

print("\n=== PyTorch Model Created ===")
vocab_size = 5000
pytorch_model = LSTMClassifier(vocab_size, 64, 128, 1)
print(pytorch_model)
```

Practice:
- [ ] Implement both LSTM and CNN models
- [ ] Add attention mechanism to LSTM
- [ ] Experiment with different architectures
- [ ] Implement early stopping

---

 Day 4: Transfer Learning with Transformers
Lecture: [04_transfer_learning_nlp.md](./lecture_notes/04_transfer_learning_nlp.md)

Topics:
- Why transfer learning in NLP
- BERT for classification
- Fine-tuning strategies
- HuggingFace Transformers library
- Handling class imbalance

Hands-On:
```python
from transformers import (
    BertForSequenceClassification,
    BertTokenizer,
    RobertaForSequenceClassification,
    RobertaTokenizer,
    Trainer,
    TrainingArguments,
    DataCollatorWithPadding
)
from datasets import Dataset
import torch
import numpy as np
from sklearn.metrics import accuracy_score, precision_recall_fscore_support

 Load pre-trained BERT
model_name = 'bert-base-uncased'
tokenizer = BertTokenizer.from_pretrained(model_name)
model = BertForSequenceClassification.from_pretrained(
    model_name,
    num_labels=2,   Binary classification
    id2label={0: 'Negative', 1: 'Positive'},
    label2id={'Negative': 0, 'Positive': 1}
)

 Prepare data for transformers
def tokenize_function(examples):
    return tokenizer(
        examples['text'],
        padding='max_length',
        truncation=True,
        max_length=128
    )

 Create dataset (in real scenario, use larger dataset)
train_data = Dataset.from_dict({'text': X_train, 'label': y_train})
test_data = Dataset.from_dict({'text': X_test, 'label': y_test})

tokenized_train = train_data.map(tokenize_function, batched=True)
tokenized_test = test_data.map(tokenize_function, batched=True)

 Define metrics
def compute_metrics(pred):
    labels = pred.label_ids
    preds = pred.predictions.argmax(-1)
    precision, recall, f1, _ = precision_recall_fscore_support(
        labels, preds, average='binary'
    )
    acc = accuracy_score(labels, preds)
    return {
        'accuracy': acc,
        'f1': f1,
        'precision': precision,
        'recall': recall
    }

 Training arguments
training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    warmup_steps=500,
    weight_decay=0.01,
    logging_dir='./logs',
    logging_steps=10,
    eval_strategy='epoch',
    save_strategy='epoch',
    load_best_model_at_end=True,
)

 Trainer
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_train,
    eval_dataset=tokenized_test,
    compute_metrics=compute_metrics,
)

 Train (commented out for demo)
 trainer.train()

 Evaluation
 results = trainer.evaluate()
 print(results)

 Direct inference example
def predict_with_bert(text, model, tokenizer):
    inputs = tokenizer(
        text,
        return_tensors='pt',
        truncation=True,
        padding=True,
        max_length=128
    )
    
    with torch.no_grad():
        outputs = model(inputs)
        predictions = torch.nn.functional.softmax(outputs.logits, dim=-1)
    
    predicted_class = torch.argmax(predictions, dim=-1).item()
    confidence = predictions[0][predicted_class].item()
    
    sentiment = 'Positive' if predicted_class == 1 else 'Negative'
    return sentiment, confidence

 Test
sample_text = "This product exceeded my expectations! Highly recommended."
sentiment, conf = predict_with_bert(sample_text, model, tokenizer)
print(f"BERT Prediction: {sentiment} (confidence: {conf:.3f})")

 Compare different transformer models
models_to_compare = [
    ('bert-base-uncased', BertForSequenceClassification, BertTokenizer),
    ('distilbert-base-uncased', None, None),   Use Auto classes
    ('roberta-base', RobertaForSequenceClassification, RobertaTokenizer),
]

print("\n=== Model Comparison Setup ===")
for name, model_cls, tok_cls in models_to_compare:
    print(f"Model: {name}")
    print(f"  Model class: {model_cls.__name__ if model_cls else 'AutoModel'}")
```

Practice:
- [ ] Fine-tune BERT for sentiment classification
- [ ] Compare BERT vs DistilBERT vs RoBERTa
- [ ] Implement class weighting for imbalance
- [ ] Create custom classification head

---

 Day 5: Multi-Class, Multi-Label & Deployment
Lecture: [05_multiclass_multilabel.md](./lecture_notes/05_multiclass_multilabel.md) | [06_deployment.md](./lecture_notes/06_deployment.md)

Topics:
- Multi-class classification (softmax)
- Multi-label classification (sigmoid)
- Hierarchical classification
- Model deployment options
- API creation with FastAPI

Hands-On:
```python
 Multi-Class Classification Example
from sklearn.preprocessing import LabelEncoder
from tensorflow.keras.utils import to_categorical

 Sample multi-class data (e.g., news categories)
news_texts = [
    "Stock market hits record high today",
    "New cancer treatment shows promise",
    "Champions League final tomorrow",
    "iPhone 16 rumors and leaks",
    "Election results announced",
]
news_labels = ['business', 'health', 'sports', 'tech', 'politics']

 Encode labels
label_encoder = LabelEncoder()
y_encoded = label_encoder.fit_transform(news_labels)
y_categorical = to_categorical(y_encoded)

print("Multi-class encoding:")
for text, label, encoded in zip(news_texts, news_labels, y_encoded):
    print(f"  {label:<10} | {encoded} | {text[:40]}...")

 Multi-Label Classification
from sklearn.preprocessing import MultiLabelBinarizer

 Sample multi-label data
texts_multi = [
    "This movie is both funny and emotional",
    "Great action scenes but poor acting",
    "Documentary about climate change and wildlife",
]
labels_multi = [
    ['comedy', 'drama'],
    ['action', 'drama'],
    ['documentary', 'nature'],
]

mlb = MultiLabelBinarizer()
y_multi = mlb.fit_transform(labels_multi)
print("\nMulti-label encoding:")
print(f"Classes: {mlb.classes_}")
print(f"Encoded:\n{y_multi}")

 FastAPI Deployment
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import uvicorn

app = FastAPI(title="Sentiment Analysis API", version="1.0")

class TextInput(BaseModel):
    text: str
    model_type: str = "ml"   'ml', 'dl', or 'transformer'

class SentimentResponse(BaseModel):
    text: str
    sentiment: str
    confidence: float
    model_used: str

 Load models (in production, load once at startup)
 ml_model = load_ml_model()
 dl_model = load_dl_model()
 transformer_model = load_transformer()

@app.post("/predict", response_model=SentimentResponse)
async def predict_sentiment_api(input_data: TextInput):
    """
    Analyze sentiment of input text.
    
    - text: The text to analyze
    - model_type: 'ml' (fast), 'dl' (balanced), 'transformer' (accurate)
    """
    if not input_data.text.strip():
        raise HTTPException(status_code=400, detail="Text cannot be empty")
    
     Mock prediction (replace with actual model inference)
    if input_data.model_type == "ml":
        sentiment = "Positive" if len(input_data.text) > 20 else "Neutral"
        confidence = 0.75
    elif input_data.model_type == "transformer":
        sentiment = "Positive"
        confidence = 0.92
    else:
        sentiment = "Positive"
        confidence = 0.85
    
    return SentimentResponse(
        text=input_data.text,
        sentiment=sentiment,
        confidence=confidence,
        model_used=input_data.model_type
    )

@app.get("/health")
async def health_check():
    return {"status": "healthy", "models_loaded": True}

@app.get("/models")
async def list_models():
    return {
        "available_models": [
            {"id": "ml", "name": "Logistic Regression", "speed": "fast", "accuracy": "medium"},
            {"id": "dl", "name": "LSTM Neural Network", "speed": "medium", "accuracy": "high"},
            {"id": "transformer", "name": "BERT", "speed": "slow", "accuracy": "very high"},
        ]
    }

 Run with: uvicorn app:app --reload
print("\n=== FastAPI Application Created ===")
print("Run with: uvicorn app:app --host 0.0.0.0 --port 8000")

 Complete Pipeline Example
class SentimentAnalysisPipeline:
    def __init__(self, model_type='ml'):
        self.model_type = model_type
        self.vectorizer = None
        self.model = None
        self.tokenizer = None
        self.transformer_model = None
        
    def train(self, texts, labels):
        """Train the selected model type."""
        if self.model_type == 'ml':
            self.vectorizer = TfidfVectorizer(ngram_range=(1, 2))
            X = self.vectorizer.fit_transform(texts)
            self.model = LogisticRegression(max_iter=1000)
            self.model.fit(X, labels)
        elif self.model_type == 'transformer':
             Would use Trainer API here
            pass
        
        return self
    
    def predict(self, texts):
        """Make predictions."""
        if self.model_type == 'ml':
            X = self.vectorizer.transform(texts)
            predictions = self.model.predict(X)
            probabilities = self.model.predict_proba(X)
            return predictions, probabilities
         ... implement other model types
    
    def save(self, path):
        """Save model to disk."""
        import pickle
        with open(path, 'wb') as f:
            pickle.dump({
                'model_type': self.model_type,
                'vectorizer': self.vectorizer,
                'model': self.model
            }, f)
    
    @classmethod
    def load(cls, path):
        """Load model from disk."""
        import pickle
        with open(path, 'rb') as f:
            data = pickle.load(f)
        
        pipeline = cls(data['model_type'])
        pipeline.vectorizer = data['vectorizer']
        pipeline.model = data['model']
        return pipeline

 Usage
print("\n=== Complete Pipeline Example ===")
pipeline = SentimentAnalysisPipeline(model_type='ml')
 pipeline.train(X_train, y_train)
 predictions = pipeline.predict(["This is great!"])
 pipeline.save('sentiment_model.pkl')
 loaded_pipeline = SentimentAnalysisPipeline.load('sentiment_model.pkl')
```

Practice:
- [ ] Build multi-class news classifier
- [ ] Implement multi-label movie genre classifier
- [ ] Create FastAPI deployment
- [ ] Add model versioning and monitoring

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_sentiment_analysis_basics.md](./lecture_notes/01_sentiment_analysis_basics.md) - Sentiment fundamentals
   - [02_ml_text_classification.md](./lecture_notes/02_ml_text_classification.md) - ML approaches
   - [03_dl_text_classification.md](./lecture_notes/03_dl_text_classification.md) - Deep learning
   - [04_transfer_learning_nlp.md](./lecture_notes/04_transfer_learning_nlp.md) - BERT fine-tuning
   - [05_multiclass_multilabel.md](./lecture_notes/05_multiclass_multilabel.md) - Advanced classification
   - [06_deployment.md](./lecture_notes/06_deployment.md) - Production deployment

2. External Resources:
   - [Sentiment Analysis Guide](https://www.kaggle.com/code/paoloripamonti/sentiment-analysis) - Kaggle tutorial
   - [HuggingFace Course](https://huggingface.co/course/) - Transformers course
   - [FastAPI Docs](https://fastapi.tiangolo.com/) - API development
   - [Text Classification Survey](https://arxiv.org/abs/2004.03705) - Research paper

 Tools & Setup
```bash
 Install required packages
pip install scikit-learn tensorflow torch transformers
pip install vaderSentiment textblob fastapi uvicorn
pip install datasets   HuggingFace datasets

 For PyTorch with GPU (optional)
 pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Sentiment Analysis System (25%)
   - Lexicon-based implementation
   - ML-based classifier (Naive Bayes, SVM, LR)
   - Deep learning model (LSTM or CNN)
   - Performance comparison report

2. Text Classification Pipeline (25%)
   - Complete preprocessing pipeline
   - Feature engineering and selection
   - Model training and validation
   - Hyperparameter tuning results

3. Transformer Fine-tuning (25%)
   - BERT fine-tuned for sentiment
   - Comparison with baseline models
   - Inference optimization
   - Documentation

4. Deployment Project (25%)
   - FastAPI application
   - Model serving endpoint
   - Multi-class/multi-label support
   - API documentation

---

 🎯 Exercises

 Exercise 1: Sentiment Comparison Study
Compare all approaches on same dataset:
- VADER vs TextBlob vs ML vs DL vs Transformer
- Performance vs speed trade-offs
- Error analysis by sentiment type
- Production recommendation

 Exercise 2: Multi-Class News Classifier
Build complete news classification system:
- 5+ categories (sports, politics, tech, etc.)
- Compare ML, CNN, LSTM, BERT
- Hierarchical classification (topic → subtopic)
- Real-time inference optimization

 Exercise 3: Multi-Label Movie Classifier
Create movie genre prediction:
- Handle multiple genres per movie
- Use movie descriptions as input
- Implement label correlation analysis
- Build recommendation system

 Exercise 4: Production-Grade API
Deploy enterprise-ready API:
- Authentication and rate limiting
- Model versioning (A/B testing)
- Monitoring and logging
- Auto-scaling configuration

---

 📋 Assignment: Complete Text Classification System

 Part A: Baseline Models (20 points)
- [ ] Lexicon-based sentiment (5 pts)
- [ ] Traditional ML classifiers (7 pts)
- [ ] Feature engineering (5 pts)
- [ ] Evaluation metrics (3 pts)

 Part B: Deep Learning (25 points)
- [ ] LSTM implementation (10 pts)
- [ ] CNN implementation (10 pts)
- [ ] Model comparison (5 pts)

 Part C: Transformers (25 points)
- [ ] BERT fine-tuning (12 pts)
- [ ] Multi-class adaptation (8 pts)
- [ ] Performance optimization (5 pts)

 Part D: Deployment (20 points)
- [ ] FastAPI application (10 pts)
- [ ] Model packaging (5 pts)
- [ ] API documentation (5 pts)

 Part E: Documentation (10 points)
- [ ] Architecture diagram (3 pts)
- [ ] Performance report (4 pts)
- [ ] Deployment guide (3 pts)

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [📖 Lecture Notes](./lecture_notes/) | Classification concepts |
| [🎯 Exercises](./exercises/) | Hands-on practice |
| [📋 Assignments](./assignments/) | Graded deliverables |
| [📄 Cheatsheets](./cheatsheets/) | API quick reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Read lecture notes |
| 45 min | Implement and train models |
| 15 min | Evaluate and compare |
| 15 min | Document results |

Total Daily Time: ~1.75 hours

---

 💡 Success Tips for Week 3

1. Start Simple: Get baseline working before complex models
2. Data Quality: Clean, balanced data beats complex models
3. Compare Everything: Don't assume DL > ML > Rules
4. Cross-Validate: Always use proper validation strategy
5. Monitor Overfitting: Watch train vs validation metrics
6. Feature Engineering: Still matters even with DL
7. Production First: Design for deployment from day one
8. Version Everything: Models, data, code, configs

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `Class imbalance` | Use class weights, oversampling, or focal loss |
| `Overfitting` | Add dropout, reduce model size, more data |
| `BERT too slow` | Use DistilBERT or quantization |
| `Memory error` | Reduce batch size, use gradient accumulation |
| `Poor accuracy` | Check data quality, try different architectures |
| `API timeout` | Implement async processing, caching |

---

 🎓 Week 3 Completion Checklist

- [ ] Lexicon sentiment analyzer built
- [ ] ML classifiers trained and compared
- [ ] Deep learning models implemented
- [ ] BERT fine-tuned for task
- [ ] Multi-class/multi-label handled
- [ ] FastAPI deployed
- [ ] All models evaluated
- [ ] Documentation complete
- [ ] Exercises submitted
- [ ] Assignment delivered

---

Previous: [Week 2: Word Embeddings ←](../Week_2/README.md) | Next: [Week 4: Advanced NLP →](../Week_4/README.md)
