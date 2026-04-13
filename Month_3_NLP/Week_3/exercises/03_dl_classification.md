 Exercise 3: Deep Learning for Text Classification

 🎯 Objective
Implement deep learning models for text classification using Keras/TensorFlow and PyTorch.

---

 📋 Tasks

 Task 1: LSTM Text Classification
Implement LSTM-based text classifier with Keras.

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense, Dropout, Bidirectional
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
import numpy as np

def build_lstm_model(vocab_size: int, embedding_dim: int, max_length: int, num_classes: int = 2):
    """
    Build LSTM text classification model.
    
    Args:
        vocab_size (int): Vocabulary size
        embedding_dim (int): Embedding dimension
        max_length (int): Maximum sequence length
        num_classes (int): Number of output classes
        
    Returns:
        model: Compiled Keras model
    """
    model = Sequential([
        Embedding(vocab_size, embedding_dim, input_length=max_length),
        Bidirectional(LSTM(64, return_sequences=False)),
        Dropout(0.5),
        Dense(32, activation='relu'),
        Dropout(0.3),
        Dense(num_classes, activation='softmax' if num_classes > 2 else 'sigmoid')
    ])
    
    model.compile(
        optimizer='adam',
        loss='binary_crossentropy' if num_classes == 2 else 'categorical_crossentropy',
        metrics=['accuracy']
    )
    
    return model

def prepare_sequences(texts: list, max_length: int = 100, vocab_size: int = 5000):
    """
    Prepare sequences for LSTM training.
    
    Args:
        texts (list): List of texts
        max_length (int): Maximum sequence length
        vocab_size (int): Vocabulary size
        
    Returns:
        tuple: (padded_sequences, tokenizer)
    """
    tokenizer = Tokenizer(num_words=vocab_size, oov_token='<OOV>')
    tokenizer.fit_on_texts(texts)
    
    sequences = tokenizer.texts_to_sequences(texts)
    padded = pad_sequences(sequences, maxlen=max_length, padding='post', truncating='post')
    
    return padded, tokenizer

 Test
texts = [
    "This product is amazing and I love it",
    "Terrible quality, would not recommend",
    "Great value for money",
    "Worst purchase ever made",
    "Good product overall",
    "Absolutely terrible experience",
]  100

labels = np.array([1, 0, 1, 0, 1, 0]  100)

 Prepare data
max_length = 50
vocab_size = 5000
X, tokenizer = prepare_sequences(texts, max_length, vocab_size)

 Build model
model = build_lstm_model(vocab_size, 100, max_length, num_classes=2)
model.summary()

 Train (commented out - requires actual training)
 history = model.fit(X, labels, epochs=10, batch_size=32, validation_split=0.2, verbose=1)

print("\nLSTM model built successfully.")
print("Uncomment training code to train with actual data.")
```

---

 Task 2: CNN Text Classification
Implement CNN-based text classifier.

```python
from tensorflow.keras.layers import Conv1D, GlobalMaxPooling1D

def build_cnn_model(vocab_size: int, embedding_dim: int, max_length: int, num_classes: int = 2):
    """
    Build CNN text classification model.
    
    Args:
        vocab_size (int): Vocabulary size
        embedding_dim (int): Embedding dimension
        max_length (int): Maximum sequence length
        num_classes (int): Number of output classes
        
    Returns:
        model: Compiled Keras model
    """
    model = Sequential([
        Embedding(vocab_size, embedding_dim, input_length=max_length),
        Conv1D(128, 5, activation='relu'),
        GlobalMaxPooling1D(),
        Dense(64, activation='relu'),
        Dropout(0.5),
        Dense(32, activation='relu'),
        Dropout(0.3),
        Dense(num_classes, activation='softmax' if num_classes > 2 else 'sigmoid')
    ])
    
    model.compile(
        optimizer='adam',
        loss='binary_crossentropy' if num_classes == 2 else 'categorical_crossentropy',
        metrics=['accuracy']
    )
    
    return model

def build_cnn_multi_filter(vocab_size: int, embedding_dim: int, max_length: int, num_classes: int = 2):
    """
    Build CNN with multiple filter sizes.
    
    Args:
        vocab_size (int): Vocabulary size
        embedding_dim (int): Embedding dimension
        max_length (int): Maximum sequence length
        num_classes (int): Number of output classes
        
    Returns:
        model: Compiled Keras model
    """
    from tensorflow.keras.layers import Input, Concatenate
    from tensorflow.keras.models import Model
    
     Input layer
    inputs = Input(shape=(max_length,))
    
     Embedding
    x = Embedding(vocab_size, embedding_dim)(inputs)
    
     Multiple filter sizes
    filter_sizes = [3, 4, 5]
    conv_outputs = []
    
    for filter_size in filter_sizes:
        conv = Conv1D(128, filter_size, activation='relu')(x)
        pool = GlobalMaxPooling1D()(conv)
        conv_outputs.append(pool)
    
     Concatenate
    if len(conv_outputs) > 1:
        x = Concatenate()(conv_outputs)
    else:
        x = conv_outputs[0]
    
     Dense layers
    x = Dense(64, activation='relu')(x)
    x = Dropout(0.5)(x)
    outputs = Dense(num_classes, activation='softmax' if num_classes > 2 else 'sigmoid')(x)
    
    model = Model(inputs=inputs, outputs=outputs)
    
    model.compile(
        optimizer='adam',
        loss='binary_crossentropy' if num_classes == 2 else 'categorical_crossentropy',
        metrics=['accuracy']
    )
    
    return model

 Test
cnn_model = build_cnn_model(vocab_size, 100, max_length, num_classes=2)
print("CNN Model:")
cnn_model.summary()

cnn_multi = build_cnn_multi_filter(vocab_size, 100, max_length, num_classes=2)
print("\nCNN Multi-Filter Model:")
cnn_multi.summary()
```

---

 Task 3: PyTorch Implementation
Implement LSTM classifier using PyTorch.

```python
import torch
import torch.nn as nn
from torch.utils.data import Dataset, DataLoader

class TextDataset(Dataset):
    """
    PyTorch Dataset for text classification.
    """
    def __init__(self, texts, labels, tokenizer, max_length):
        """
        Initialize dataset.
        
        Args:
            texts (list): List of texts
            labels (list): List of labels
            tokenizer: Tokenizer function
            max_length (int): Maximum sequence length
        """
        self.texts = texts
        self.labels = labels
        self.tokenizer = tokenizer
        self.max_length = max_length
    
    def __len__(self):
        return len(self.texts)
    
    def __getitem__(self, idx):
        text = self.texts[idx]
        label = self.labels[idx]
        
         Tokenize
        tokens = self.tokenizer(text)
        tokens = tokens[:self.max_length]
        
         Pad
        padding = [0]  (self.max_length - len(tokens))
        tokens = tokens + padding
        
        return {
            'input_ids': torch.tensor(tokens, dtype=torch.long),
            'label': torch.tensor(label, dtype=torch.long)
        }

class LSTMClassifier(nn.Module):
    """
    LSTM text classifier in PyTorch.
    """
    def __init__(self, vocab_size, embedding_dim, hidden_dim, output_dim, num_layers=2, dropout=0.5):
        """
        Initialize LSTM classifier.
        
        Args:
            vocab_size (int): Vocabulary size
            embedding_dim (int): Embedding dimension
            hidden_dim (int): Hidden dimension
            output_dim (int): Output dimension
            num_layers (int): Number of LSTM layers
            dropout (float): Dropout rate
        """
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, embedding_dim)
        self.lstm = nn.LSTM(
            embedding_dim, 
            hidden_dim, 
            num_layers=num_layers, 
            batch_first=True,
            bidirectional=True,
            dropout=dropout if num_layers > 1 else 0
        )
        self.dropout = nn.Dropout(dropout)
        self.fc = nn.Linear(hidden_dim  2, output_dim)
    
    def forward(self, x):
         x shape: (batch, seq_len)
        embedded = self.dropout(self.embedding(x))
        
         LSTM output: (batch, seq_len, hidden  2)
        lstm_out, (hidden, cell) = self.lstm(embedded)
        
         Use last hidden state
        hidden = torch.cat((hidden[-2,:,:], hidden[-1,:,:]), dim=1)
        
         Fully connected
        output = self.fc(self.dropout(hidden))
        
        return output

 Simple tokenizer function
def simple_tokenizer(text: str, word_to_idx: dict) -> list:
    """
    Simple tokenizer for PyTorch.
    
    Args:
        text (str): Input text
        word_to_idx (dict): Word to index mapping
        
    Returns:
        list: Token indices
    """
    words = text.lower().split()
    return [word_to_idx.get(word, 0) for word in words]   0 = unknown

 Test
vocab_size = 1000
embedding_dim = 100
hidden_dim = 128
output_dim = 2

model = LSTMClassifier(vocab_size, embedding_dim, hidden_dim, output_dim)
print("PyTorch LSTM Model:")
print(model)

 Test forward pass
batch_size = 4
seq_length = 50
dummy_input = torch.randint(0, vocab_size, (batch_size, seq_length))
output = model(dummy_input)
print(f"\nOutput shape: {output.shape}")
```

---

 Task 4: Attention Mechanism
Add attention to LSTM model.

```python
class AttentionLSTM(nn.Module):
    """
    LSTM with attention mechanism.
    """
    def __init__(self, vocab_size, embedding_dim, hidden_dim, output_dim):
        """
        Initialize attention LSTM.
        
        Args:
            vocab_size (int): Vocabulary size
            embedding_dim (int): Embedding dimension
            hidden_dim (int): Hidden dimension
            output_dim (int): Output dimension
        """
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, embedding_dim)
        self.lstm = nn.LSTM(embedding_dim, hidden_dim, batch_first=True, bidirectional=True)
        self.attention = nn.Linear(hidden_dim  2, 1)
        self.dropout = nn.Dropout(0.5)
        self.fc = nn.Linear(hidden_dim  2, output_dim)
    
    def forward(self, x):
         Embedding
        embedded = self.dropout(self.embedding(x))
        
         LSTM
        lstm_out, _ = self.lstm(embedded)
        
         Attention weights
        attention_weights = torch.softmax(self.attention(lstm_out), dim=1)
        
         Weighted sum
        attended = torch.sum(attention_weights  lstm_out, dim=1)
        
         Output
        output = self.fc(self.dropout(attended))
        
        return output, attention_weights

 Test
attention_model = AttentionLSTM(vocab_size, embedding_dim, hidden_dim, output_dim)
print("Attention LSTM Model:")
print(attention_model)

output, attention = attention_model(dummy_input)
print(f"\nOutput shape: {output.shape}")
print(f"Attention weights shape: {attention.shape}")
```

---

 Task 5: Model Comparison
Compare DL architectures on same dataset.

```python
def compare_dl_models(X_train, y_train, X_test, y_test, vocab_size, max_length):
    """
    Compare different DL models.
    
    Args:
        X_train: Training sequences
        y_train: Training labels
        X_test: Test sequences
        y_test: Test labels
        vocab_size (int): Vocabulary size
        max_length (int): Maximum sequence length
        
    Returns:
        dict: Comparison results
    """
    models = {
        'LSTM': build_lstm_model(vocab_size, 100, max_length),
        'CNN': build_cnn_model(vocab_size, 100, max_length),
        'CNN Multi-Filter': build_cnn_multi_filter(vocab_size, 100, max_length),
    }
    
    results = {}
    
    for name, model in models.items():
         Train (simplified - in practice use proper training loop)
         history = model.fit(X_train, y_train, epochs=5, batch_size=32, validation_split=0.2, verbose=0)
        
         Evaluate
         loss, accuracy = model.evaluate(X_test, y_test, verbose=0)
        
         Placeholder for demo
        results[name] = {
            'parameters': model.count_params(),
             'accuracy': accuracy,
             'loss': loss
        }
    
    return results

 Test
 results = compare_dl_models(X, labels, X, labels, vocab_size, max_length)
 print("DL Model Comparison:")
 for name, metrics in results.items():
     print(f"{name}: Params={metrics['parameters']}, Accuracy={metrics['accuracy']:.3f}")

print("\nNote: Model comparison requires actual training data.")
print("Uncomment the code above to run with your dataset.")
```

---

 ✅ Completion Checklist

- [ ] LSTM model implemented
- [ ] CNN model implemented
- [ ] PyTorch implementation working
- [ ] Attention mechanism added
- [ ] Model comparison framework created
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete DL text classification system:

```python
class DLTextClassifier:
    """
    Complete deep learning text classification system.
    """
    def __init__(self, architecture='lstm'):
        """
        Initialize classifier.
        
        Args:
            architecture (str): 'lstm', 'cnn', or 'attention'
        """
         Your implementation
        pass
    
    def prepare_data(self, texts, labels, max_length=100, vocab_size=5000):
        """
        Prepare data for training.
        
        Args:
            texts (list): List of texts
            labels (list): List of labels
            max_length (int): Maximum sequence length
            vocab_size (int): Vocabulary size
            
        Returns:
            prepared data
        """
         Your implementation
        pass
    
    def build_model(self, vocab_size, embedding_dim, max_length, num_classes):
        """
        Build model based on architecture.
        
        Args:
            vocab_size (int): Vocabulary size
            embedding_dim (int): Embedding dimension
            max_length (int): Maximum sequence length
            num_classes (int): Number of classes
            
        Returns:
            model
        """
         Your implementation
        pass
    
    def train(self, X_train, y_train, X_val=None, y_val=None, epochs=10):
        """
        Train the model.
        
        Args:
            X_train: Training data
            y_train: Training labels
            X_val: Validation data
            y_val: Validation labels
            epochs (int): Number of epochs
            
        Returns:
            training history
        """
         Your implementation
        pass
    
    def predict(self, X):
        """
        Make predictions.
        
        Args:
            X: Input data
            
        Returns:
            predictions
        """
         Your implementation
        pass
    
    def save(self, path):
        """Save model."""
         Your implementation
        pass
    
    def load(self, path):
        """Load model."""
         Your implementation
        pass
```
