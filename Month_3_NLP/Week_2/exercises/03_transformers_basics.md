 Exercise 3: Transformers & BERT Basics

 🎯 Objective
Implement and use transformer models for NLP tasks using HuggingFace.

---

 📋 Tasks

 Task 1: BERT Tokenization
Understand BERT's WordPiece tokenization.

```python
from transformers import BertTokenizer

def analyze_bert_tokenization(text: str, tokenizer: BertTokenizer) -> Dict:
    """
    Analyze BERT tokenization in detail.
    
    Args:
        text (str): Input text
        tokenizer (BertTokenizer): BERT tokenizer
        
    Returns:
        dict: Tokenization analysis
    """
     Tokenize
    tokens = tokenizer.tokenize(text)
    
     Convert to IDs
    ids = tokenizer.convert_tokens_to_ids(tokens)
    
     Decode back
    decoded = tokenizer.decode(ids)
    
     Get special tokens
    cls_token = tokenizer.cls_token
    sep_token = tokenizer.sep_token
    pad_token = tokenizer.pad_token
    
     Full encoding with special tokens
    encoded = tokenizer(text, return_tensors='pt')
    
    return {
        'original_text': text,
        'tokens': tokens,
        'token_ids': ids,
        'decoded': decoded,
        'special_tokens': {
            'CLS': cls_token,
            'SEP': sep_token,
            'PAD': pad_token
        },
        'full_encoding': encoded
    }

 Test
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')

texts = [
    "Natural language processing is fascinating",
    "The microprocessor was invented in the 1970s",
    "COVID-19 vaccine research has progressed rapidly",
]

for text in texts:
    analysis = analyze_bert_tokenization(text, tokenizer)
    print(f"\nText: {text}")
    print(f"Tokens: {analysis['tokens']}")
    print(f"Token count: {len(analysis['tokens'])}")
    print(f"Decoded: {analysis['decoded']}")
```

Analysis Questions:
1. How does BERT handle unknown words?
2. What is the purpose of [CLS] and [SEP] tokens?
3. How does subword tokenization help with vocabulary size?

---

 Task 2: BERT Feature Extraction
Extract features from BERT for downstream tasks.

```python
import torch
from transformers import BertModel, BertTokenizer

def extract_bert_features(text: str, layer: int = -1) -> Dict:
    """
    Extract BERT features.
    
    Args:
        text (str): Input text
        layer (int): Which layer to extract (-1 for last)
        
    Returns:
        dict: Extracted features
    """
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    model = BertModel.from_pretrained('bert-base-uncased')
    
     Tokenize
    inputs = tokenizer(text, return_tensors='pt', padding=True, truncation=True, max_length=512)
    
     Get embeddings
    with torch.no_grad():
        outputs = model(inputs)
    
     Different pooling strategies
    last_hidden_state = outputs.last_hidden_state   (batch, seq_len, hidden)
    pooler_output = outputs.pooler_output   (batch, hidden)
    
     CLS token embedding
    cls_embedding = last_hidden_state[:, 0, :]
    
     Mean pooling
    mean_embedding = last_hidden_state.mean(dim=1)
    
     Max pooling
    max_embedding, _ = last_hidden_state.max(dim=1)
    
    return {
        'cls_embedding': cls_embedding,
        'pooler_output': pooler_output,
        'mean_embedding': mean_embedding,
        'max_embedding': max_embedding,
        'all_hidden_states': last_hidden_state,
        'attention_mask': inputs['attention_mask']
    }

 Test
text = "Natural language processing has revolutionized how machines understand human language."
features = extract_bert_features(text)

print("Feature Shapes:")
print(f"CLS embedding: {features['cls_embedding'].shape}")
print(f"Pooler output: {features['pooler_output'].shape}")
print(f"Mean embedding: {features['mean_embedding'].shape}")
print(f"Max embedding: {features['max_embedding'].shape}")
```

---

 Task 3: BERT Pipeline Tasks
Use HuggingFace pipelines for common NLP tasks.

```python
from transformers import pipeline

def test_bert_pipelines():
    """
    Test various BERT pipelines.
    """
     Sentiment Analysis
    sentiment = pipeline('sentiment-analysis')
    text = "I absolutely love this product! It exceeded all my expectations."
    result = sentiment(text)
    print(f"Sentiment Analysis: {result}")
    
     Named Entity Recognition
    ner = pipeline('ner', aggregation_strategy='simple')
    text = "Apple Inc. was founded by Steve Jobs in Cupertino, California."
    entities = ner(text)
    print(f"\nNamed Entities: {entities}")
    
     Question Answering
    qa = pipeline('question-answering')
    context = "BERT is a transformer-based machine learning technique for natural language processing pre-training developed by Google."
    question = "Who developed BERT?"
    answer = qa(question=question, context=context)
    print(f"\nQuestion Answering: {answer}")
    
     Text Classification
    classifier = pipeline('zero-shot-classification')
    text = "The new AI model can generate human-like text."
    labels = ['technology', 'sports', 'politics', 'entertainment']
    result = classifier(text, labels)
    print(f"\nZero-shot Classification: {result}")
    
     Feature Extraction
    feature_extractor = pipeline('feature-extraction')
    features = feature_extractor(text)
    print(f"\nFeature extraction shape: {len(features)} x {len(features[0])}")

 Test
test_bert_pipelines()
```

---

 Task 4: Fine-tuning BERT for Classification
Fine-tune BERT on a simple classification task.

```python
from transformers import BertForSequenceClassification, BertTokenizer, Trainer, TrainingArguments
from datasets import Dataset
import torch
import numpy as np

def prepare_bert_classification_data(texts: list, labels: list) -> Dataset:
    """
    Prepare data for BERT classification.
    
    Args:
        texts (list): List of texts
        labels (list): List of labels
        
    Returns:
        Dataset: HuggingFace Dataset
    """
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    
    def tokenize_function(examples):
        return tokenizer(
            examples['text'],
            padding='max_length',
            truncation=True,
            max_length=128
        )
    
     Create dataset
    data_dict = {'text': texts, 'label': labels}
    dataset = Dataset.from_dict(data_dict)
    
     Tokenize
    tokenized_dataset = dataset.map(tokenize_function, batched=True)
    
    return tokenized_dataset

def fine_tune_bert(train_texts: list, train_labels: list, num_epochs: int = 3):
    """
    Fine-tune BERT for classification.
    
    Args:
        train_texts (list): Training texts
        train_labels (list): Training labels
        num_epochs (int): Number of training epochs
        
    Returns:
        trained model
    """
     Prepare data
    train_dataset = prepare_bert_classification_data(train_texts, train_labels)
    
     Split for validation
    train_dataset = train_dataset.train_test_split(test_size=0.2)
    
     Load model
    model = BertForSequenceClassification.from_pretrained(
        'bert-base-uncased',
        num_labels=len(set(train_labels))
    )
    
     Training arguments
    training_args = TrainingArguments(
        output_dir='./results',
        num_train_epochs=num_epochs,
        per_device_train_batch_size=8,
        per_device_eval_batch_size=8,
        warmup_steps=500,
        weight_decay=0.01,
        logging_dir='./logs',
        evaluation_strategy='epoch',
        save_strategy='epoch',
        load_best_model_at_end=True,
    )
    
     Trainer
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=train_dataset['train'],
        eval_dataset=train_dataset['test'],
    )
    
     Train
    trainer.train()
    
    return model

 Example usage (commented out - requires actual data)
 train_texts = ["This is positive", "This is negative"]  100
 train_labels = [1, 0]  100
 model = fine_tune_bert(train_texts, train_labels, num_epochs=2)

print("Note: Fine-tuning requires labeled data and may take significant time.")
print("Uncomment the code above to run with your own dataset.")
```

---

 Task 5: Attention Visualization
Visualize BERT's attention mechanism.

```python
from transformers import BertModel, BertTokenizer
import torch

def extract_attention_weights(text: str) -> torch.Tensor:
    """
    Extract attention weights from BERT.
    
    Args:
        text (str): Input text
        
    Returns:
        tensor: Attention weights
    """
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    model = BertModel.from_pretrained('bert-base-uncased', output_attentions=True)
    
    inputs = tokenizer(text, return_tensors='pt')
    
    with torch.no_grad():
        outputs = model(inputs)
    
     outputs.attentions is a tuple of 12 layers
     Each layer has shape (batch, num_heads, seq_len, seq_len)
    return outputs.attentions

def analyze_attention_patterns(text: str):
    """
    Analyze attention patterns in text.
    
    Args:
        text (str): Input text
    """
    attentions = extract_attention_weights(text)
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    tokens = tokenizer.tokenize(text)
    
     Average attention across all layers and heads
    avg_attention = torch.stack(attentions).mean(dim=0).mean(dim=1)   (1, seq_len, seq_len)
    avg_attention = avg_attention[0]   (seq_len, seq_len)
    
    print(f"Text: {text}")
    print(f"Tokens: {tokens}")
    print(f"\nAttention shape: {avg_attention.shape}")
    
     Find which tokens attend to which
    print("\nTop attention patterns:")
    for i, token in enumerate(tokens):
         Get attention weights for this token
        attn_weights = avg_attention[i]
        
         Get top 3 attended tokens
        top_indices = attn_weights.argsort()[-3:][::-1]
        
        print(f"\n{token} attends to:")
        for idx in top_indices:
            print(f"  {tokens[idx]}: {attn_weights[idx].item():.3f}")

 Test
text = "The cat sat on the mat"
analyze_attention_patterns(text)
```

---

 ✅ Completion Checklist

- [ ] BERT tokenization analyzed
- [ ] BERT features extracted
- [ ] BERT pipelines tested
- [ ] BERT fine-tuning implemented
- [ ] Attention visualization created
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete BERT-based text classification system:

```python
class BERTClassifier:
    """
    Complete BERT classification system.
    """
    def __init__(self, model_name='bert-base-uncased', num_labels=2):
        """
        Initialize BERT classifier.
        
        Args:
            model_name (str): Pre-trained model name
            num_labels (int): Number of classification labels
        """
         Your implementation
        pass
    
    def prepare_data(self, texts: list, labels: list = None):
        """
        Prepare data for training/inference.
        
        Args:
            texts (list): List of texts
            labels (list): Optional labels
            
        Returns:
            processed data
        """
         Your implementation
        pass
    
    def train(self, train_texts, train_labels, val_texts=None, val_labels=None):
        """
        Train the classifier.
        
        Args:
            train_texts (list): Training texts
            train_labels (list): Training labels
            val_texts (list): Validation texts
            val_labels (list): Validation labels
            
        Returns:
            training history
        """
         Your implementation
        pass
    
    def predict(self, texts: list):
        """
        Make predictions.
        
        Args:
            texts (list): List of texts
            
        Returns:
            predictions
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
    
    def interpret(self, text: str):
        """
        Interpret prediction with attention visualization.
        
        Args:
            text (str): Input text
            
        Returns:
            interpretation
        """
         Your implementation
        pass
```
