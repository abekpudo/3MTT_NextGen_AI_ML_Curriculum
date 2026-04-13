 Exercise 4: Transformer Fine-tuning for Classification

 🎯 Objective
Fine-tune transformer models (BERT, RoBERTa) for text classification tasks.

---

 📋 Tasks

 Task 1: BERT Fine-tuning Setup
Set up BERT fine-tuning pipeline with HuggingFace.

```python
from transformers import (
    BertForSequenceClassification,
    BertTokenizer,
    Trainer,
    TrainingArguments,
    DataCollatorWithPadding
)
from datasets import Dataset
import torch
import numpy as np
from sklearn.metrics import accuracy_score, precision_recall_fscore_support

def prepare_bert_dataset(texts, labels, tokenizer):
    """
    Prepare dataset for BERT fine-tuning.
    
    Args:
        texts (list): List of texts
        labels (list): List of labels
        tokenizer: BERT tokenizer
        
    Returns:
        Dataset: HuggingFace Dataset
    """
    def tokenize_function(examples):
        return tokenizer(
            examples['text'],
            padding='max_length',
            truncation=True,
            max_length=128
        )
    
    data_dict = {'text': texts, 'label': labels}
    dataset = Dataset.from_dict(data_dict)
    
    tokenized_dataset = dataset.map(tokenize_function, batched=True)
    
    return tokenized_dataset

def compute_metrics(eval_pred):
    """
    Compute metrics for evaluation.
    
    Args:
        eval_pred: Evaluation predictions
        
    Returns:
        dict: Metrics
    """
    labels = eval_pred.label_ids
    preds = eval_pred.predictions.argmax(-1)
    
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

def setup_bert_trainer(train_dataset, eval_dataset, model, tokenizer):
    """
    Setup BERT trainer.
    
    Args:
        train_dataset: Training dataset
        eval_dataset: Evaluation dataset
        model: BERT model
        tokenizer: BERT tokenizer
        
    Returns:
        Trainer: Configured trainer
    """
    training_args = TrainingArguments(
        output_dir='./results',
        num_train_epochs=3,
        per_device_train_batch_size=16,
        per_device_eval_batch_size=16,
        warmup_steps=500,
        weight_decay=0.01,
        logging_dir='./logs',
        logging_steps=10,
        evaluation_strategy='epoch',
        save_strategy='epoch',
        load_best_model_at_end=True,
    )
    
    data_collator = DataCollatorWithPadding(tokenizer=tokenizer)
    
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=train_dataset,
        eval_dataset=eval_dataset,
        compute_metrics=compute_metrics,
        data_collator=data_collator,
    )
    
    return trainer

 Test setup
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertForSequenceClassification.from_pretrained(
    'bert-base-uncased',
    num_labels=2
)

 Sample data
texts = ["This is positive", "This is negative"]  50
labels = [1, 0]  50

train_dataset = prepare_bert_dataset(texts, labels, tokenizer)
train_split = train_dataset.train_test_split(test_size=0.2)

trainer = setup_bert_trainer(
    train_split['train'],
    train_split['test'],
    model,
    tokenizer
)

print("BERT trainer setup complete.")
print("Ready for fine-tuning.")
```

---

 Task 2: Fine-tune BERT for Sentiment
Fine-tune BERT on sentiment classification task.

```python
def fine_tune_bert_sentiment(train_texts, train_labels, test_texts, test_labels):
    """
    Fine-tune BERT for sentiment classification.
    
    Args:
        train_texts (list): Training texts
        train_labels (list): Training labels
        test_texts (list): Test texts
        test_labels (list): Test labels
        
    Returns:
        dict: Training results
    """
     Load model and tokenizer
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    model = BertForSequenceClassification.from_pretrained(
        'bert-base-uncased',
        num_labels=2
    )
    
     Prepare datasets
    train_dataset = prepare_bert_dataset(train_texts, train_labels, tokenizer)
    test_dataset = prepare_bert_dataset(test_texts, test_labels, tokenizer)
    
     Setup trainer
    trainer = setup_bert_trainer(train_dataset, test_dataset, model, tokenizer)
    
     Train
    print("Starting BERT fine-tuning...")
    trainer.train()
    
     Evaluate
    eval_results = trainer.evaluate()
    
    return {
        'eval_results': eval_results,
        'model': model,
        'tokenizer': tokenizer
    }

 Test (commented out - requires actual data)
 texts = ["I love this product", "This is terrible"]  100
 labels = [1, 0]  100

 results = fine_tune_bert_sentiment(texts, labels, texts, labels)
 print("Fine-tuning complete:")
 print(results['eval_results'])

print("\nNote: Fine-tuning requires labeled data and GPU for efficiency.")
print("Uncomment the code above to run with your dataset.")
```

---

 Task 3: Compare Transformer Models
Compare BERT, RoBERTa, and DistilBERT.

```python
from transformers import (
    RobertaForSequenceClassification,
    RobertaTokenizer,
    DistilBertForSequenceClassification,
    DistilBertTokenizer
)

def compare_transformers(texts, labels):
    """
    Compare different transformer models.
    
    Args:
        texts (list): List of texts
        labels (list): List of labels
        
    Returns:
        dict: Comparison results
    """
    models = {
        'BERT': (BertForSequenceClassification, BertTokenizer, 'bert-base-uncased'),
        'RoBERTa': (RobertaForSequenceClassification, RobertaTokenizer, 'roberta-base'),
        'DistilBERT': (DistilBertForSequenceClassification, DistilBertTokenizer, 'distilbert-base-uncased'),
    }
    
    results = {}
    
    for name, (model_class, tokenizer_class, model_name) in models.items():
        print(f"\nProcessing {name}...")
        
         Load
        tokenizer = tokenizer_class.from_pretrained(model_name)
        model = model_class.from_pretrained(model_name, num_labels=2)
        
         Prepare data
        dataset = prepare_bert_dataset(texts, labels, tokenizer)
        
         Setup trainer
        train_split = dataset.train_test_split(test_size=0.2)
        trainer = setup_bert_trainer(
            train_split['train'],
            train_split['test'],
            model,
            tokenizer
        )
        
         Quick train (fewer epochs for comparison)
        print(f"Training {name}...")
         trainer.train()   Commented for demo
        
         Evaluate
         eval_results = trainer.evaluate()
        
         Store results (placeholder)
        results[name] = {
            'model': model,
            'tokenizer': tokenizer,
            'parameters': sum(p.numel() for p in model.parameters())
        }
    
    return results

 Test
sample_texts = ["Positive text", "Negative text"]  20
sample_labels = [1, 0]  20

 results = compare_transformers(sample_texts, sample_labels)
 print("\nModel Comparison:")
 for name, info in results.items():
     print(f"{name}: {info['parameters']:,} parameters")

print("\nNote: Model comparison requires significant compute resources.")
print("Uncomment the code above to run comparison.")
```

---

 Task 4: Multi-class Classification
Fine-tune BERT for multi-class tasks.

```python
def fine_tune_bert_multiclass(train_texts, train_labels, test_texts, test_labels, num_classes):
    """
    Fine-tune BERT for multi-class classification.
    
    Args:
        train_texts (list): Training texts
        train_labels (list): Training labels
        test_texts (list): Test texts
        test_labels (list): Test labels
        num_classes (int): Number of classes
        
    Returns:
        dict: Training results
    """
     Load model with correct number of classes
    tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
    model = BertForSequenceClassification.from_pretrained(
        'bert-base-uncased',
        num_labels=num_classes
    )
    
     Prepare datasets
    train_dataset = prepare_bert_dataset(train_texts, train_labels, tokenizer)
    test_dataset = prepare_bert_dataset(test_texts, test_labels, tokenizer)
    
     Setup trainer
    trainer = setup_bert_trainer(train_dataset, test_dataset, model, tokenizer)
    
     Train
    print(f"Training BERT for {num_classes}-class classification...")
     trainer.train()
    
     Evaluate
     eval_results = trainer.evaluate()
    
    return {
        'model': model,
        'tokenizer': tokenizer
    }

 Test - Multi-class example
texts_multi = [
    "Stock market news",
    "Sports update",
    "Technology trends",
    "Political developments",
]  25

labels_multi = [0, 1, 2, 3]  25   4 classes

 results = fine_tune_bert_multiclass(texts_multi, labels_multi, texts_multi, labels_multi, 4)
 print("Multi-class fine-tuning complete.")

print("\nNote: Multi-class fine-tuning requires labeled multi-class data.")
print("Uncomment the code above to run with your dataset.")
```

---

 Task 5: Inference Optimization
Optimize BERT inference for production.

```python
import torch
from transformers import pipeline

def optimize_bert_inference(model, tokenizer):
    """
    Optimize BERT model for faster inference.
    
    Args:
        model: BERT model
        tokenizer: BERT tokenizer
        
    Returns:
        tuple: Optimized model and tokenizer
    """
     Move to GPU if available
    device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
    model = model.to(device)
    
     Set to evaluation mode
    model.eval()
    
     Enable gradient checkpointing for memory efficiency
    if hasattr(model, 'gradient_checkpointing_enable'):
        model.gradient_checkpointing_enable()
    
    return model, tokenizer

def batch_inference(texts, model, tokenizer, batch_size=32):
    """
    Perform batch inference efficiently.
    
    Args:
        texts (list): List of texts
        model: BERT model
        tokenizer: BERT tokenizer
        batch_size (int): Batch size
        
    Returns:
        list: Predictions
    """
    predictions = []
    
    model.eval()
    device = next(model.parameters()).device
    
    for i in range(0, len(texts), batch_size):
        batch_texts = texts[i:i+batch_size]
        
         Tokenize
        inputs = tokenizer(
            batch_texts,
            return_tensors='pt',
            padding=True,
            truncation=True,
            max_length=128
        )
        
         Move to device
        inputs = {k: v.to(device) for k, v in inputs.items()}
        
         Inference
        with torch.no_grad():
            outputs = model(inputs)
            preds = torch.nn.functional.softmax(outputs.logits, dim=-1)
            batch_predictions = preds.argmax(dim=-1).cpu().tolist()
        
        predictions.extend(batch_predictions)
    
    return predictions

 Use HuggingFace pipeline for optimized inference
def create_sentiment_pipeline():
    """
    Create optimized sentiment analysis pipeline.
    
    Returns:
        pipeline: Sentiment analysis pipeline
    """
    classifier = pipeline(
        'sentiment-analysis',
        model='distilbert-base-uncased-finetuned-sst-2-english',
        device=0 if torch.cuda.is_available() else -1,
        batch_size=32
    )
    
    return classifier

 Test
pipeline = create_sentiment_pipeline()

texts = [
    "I absolutely love this product!",
    "This is terrible quality.",
    "It's okay, nothing special.",
]

results = pipeline(texts)
print("Pipeline Results:")
for text, result in zip(texts, results):
    print(f"'{text}' -> {result['label']} ({result['score']:.3f})")
```

---

 ✅ Completion Checklist

- [ ] BERT fine-tuning setup complete
- [ ] Sentiment classification fine-tuning implemented
- [ ] Transformer model comparison framework created
- [ ] Multi-class classification implemented
- [ ] Inference optimization working
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete transformer fine-tuning system:

```python
class TransformerFineTuner:
    """
    Complete transformer fine-tuning system.
    """
    def __init__(self, model_name='bert-base-uncased', num_labels=2):
        """
        Initialize fine-tuner.
        
        Args:
            model_name (str): Pre-trained model name
            num_labels (int): Number of output labels
        """
         Your implementation
        pass
    
    def prepare_data(self, texts, labels, test_size=0.2):
        """
        Prepare data for fine-tuning.
        
        Args:
            texts (list): List of texts
            labels (list): List of labels
            test_size (float): Test split ratio
            
        Returns:
            datasets
        """
         Your implementation
        pass
    
    def fine_tune(self, train_dataset, eval_dataset, epochs=3):
        """
        Fine-tune the model.
        
        Args:
            train_dataset: Training dataset
            eval_dataset: Evaluation dataset
            epochs (int): Number of epochs
            
        Returns:
            training history
        """
         Your implementation
        pass
    
    def predict(self, texts):
        """
        Make predictions.
        
        Args:
            texts (list): List of texts
            
        Returns:
            predictions
        """
         Your implementation
        pass
    
    def save_model(self, path):
        """Save fine-tuned model."""
         Your implementation
        pass
    
    def load_model(self, path):
        """Load fine-tuned model."""
         Your implementation
        pass
    
    def export_onnx(self, path):
        """Export model to ONNX for deployment."""
         Your implementation
        pass
```
