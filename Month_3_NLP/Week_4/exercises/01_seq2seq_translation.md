 Exercise 1: Sequence-to-Sequence Translation

 🎯 Objective
Build a sequence-to-sequence model for machine translation.

---

 📋 Tasks

 Task 1: Encoder-Decoder Architecture
Implement basic encoder-decoder model.

```python
import tensorflow as tf
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, Embedding

def build_encoder_decoder(vocab_size, embedding_dim, latent_dim):
    """
    Build encoder-decoder architecture.
    
    Args:
        vocab_size (int): Vocabulary size
        embedding_dim (int): Embedding dimension
        latent_dim (int): LSTM hidden dimension
        
    Returns:
        tuple: (encoder_model, decoder_model, full_model)
    """
     Encoder
    encoder_inputs = Input(shape=(None,))
    encoder_embedding = Embedding(vocab_size, embedding_dim)(encoder_inputs)
    encoder_lstm = LSTM(latent_dim, return_state=True)
    encoder_outputs, state_h, state_c = encoder_lstm(encoder_embedding)
    encoder_states = [state_h, state_c]
    
     Decoder
    decoder_inputs = Input(shape=(None,))
    decoder_embedding = Embedding(vocab_size, embedding_dim)(decoder_inputs)
    decoder_lstm = LSTM(latent_dim, return_sequences=True, return_state=True)
    decoder_outputs, _, _ = decoder_lstm(decoder_embedding, initial_state=encoder_states)
    decoder_dense = Dense(vocab_size, activation='softmax')
    decoder_outputs = decoder_dense(decoder_outputs)
    
     Full model
    model = Model([encoder_inputs, decoder_inputs], decoder_outputs)
    
     Inference encoder
    encoder_model = Model(encoder_inputs, encoder_states)
    
     Inference decoder
    decoder_state_input_h = Input(shape=(latent_dim,))
    decoder_state_input_c = Input(shape=(latent_dim,))
    decoder_states_inputs = [decoder_state_input_h, decoder_state_input_c]
    
    decoder_outputs, state_h, state_c = decoder_lstm(
        decoder_embedding, initial_state=decoder_states_inputs
    )
    decoder_states = [state_h, state_c]
    decoder_outputs = decoder_dense(decoder_outputs)
    
    decoder_model = Model(
        [decoder_inputs] + decoder_states_inputs,
        [decoder_outputs] + decoder_states
    )
    
    return encoder_model, decoder_model, model

 Build models
vocab_size = 10000
embedding_dim = 256
latent_dim = 512

encoder, decoder, model = build_encoder_decoder(vocab_size, embedding_dim, latent_dim)

model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])

print("Encoder model:")
encoder.summary()
print("\nFull model:")
model.summary()
```

---

 Task 2: Data Preparation for Translation
Prepare parallel corpus for training.

```python
import numpy as np
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

class TranslationDataPreparer:
    """
    Prepare data for sequence-to-sequence translation.
    """
    def __init__(self, max_vocab_size=10000, max_seq_length=50):
        """
        Initialize data preparer.
        
        Args:
            max_vocab_size (int): Maximum vocabulary size
            max_seq_length (int): Maximum sequence length
        """
        self.max_vocab_size = max_vocab_size
        self.max_seq_length = max_seq_length
        self.source_tokenizer = Tokenizer(num_words=max_vocab_size)
        self.target_tokenizer = Tokenizer(num_words=max_vocab_size)
    
    def fit(self, source_texts, target_texts):
        """
        Fit tokenizers on texts.
        
        Args:
            source_texts (list): Source language texts
            target_texts (list): Target language texts
        """
        self.source_tokenizer.fit_on_texts(source_texts)
        self.target_tokenizer.fit_on_texts(target_texts)
    
    def prepare_sequences(self, source_texts, target_texts):
        """
        Prepare sequences for training.
        
        Args:
            source_texts (list): Source texts
            target_texts (list): Target texts
            
        Returns:
            tuple: (encoder_input, decoder_input, decoder_target)
        """
         Tokenize
        source_sequences = self.source_tokenizer.texts_to_sequences(source_texts)
        target_sequences = self.target_tokenizer.texts_to_sequences(target_texts)
        
         Pad
        encoder_input = pad_sequences(source_sequences, maxlen=self.max_seq_length, padding='post')
        
         Add start token to target input
        decoder_input = [[1] + seq[:-1] for seq in target_sequences]   1 = START token
        decoder_input = pad_sequences(decoder_input, maxlen=self.max_seq_length, padding='post')
        
         One-hot encode decoder target
        decoder_target = pad_sequences(target_sequences, maxlen=self.max_seq_length, padding='post')
        decoder_target = tf.keras.utils.to_categorical(
            decoder_target, 
            num_classes=min(len(self.target_tokenizer.word_index) + 1, self.max_vocab_size)
        )
        
        return encoder_input, decoder_input, decoder_target
    
    def decode_sequence(self, sequence, tokenizer):
        """
        Decode sequence back to text.
        
        Args:
            sequence (list): Token sequence
            tokenizer: Tokenizer used for encoding
            
        Returns:
            str: Decoded text
        """
        reverse_word_map = dict(map(reversed, tokenizer.word_index.items()))
        return ' '.join([reverse_word_map.get(i-3, '') for i in sequence if i > 0])

 Test
preparer = TranslationDataPreparer()

source_texts = [
    "hello how are you",
    "what is your name",
    "i love programming",
]

target_texts = [
    "bonjour comment allez vous",
    "quel est votre nom",
    "j adore programmer",
]

preparer.fit(source_texts, target_texts)
encoder_input, decoder_input, decoder_target = preparer.prepare_sequences(source_texts, target_texts)

print(f"Encoder input shape: {encoder_input.shape}")
print(f"Decoder input shape: {decoder_input.shape}")
print(f"Decoder target shape: {decoder_target.shape}")
```

---

 Task 3: Attention Mechanism
Add attention to seq2seq model.

```python
from tensorflow.keras.layers import Attention, Concatenate

def build_attention_seq2seq(vocab_size, embedding_dim, latent_dim):
    """
    Build seq2seq with attention mechanism.
    
    Args:
        vocab_size (int): Vocabulary size
        embedding_dim (int): Embedding dimension
        latent_dim (int): LSTM hidden dimension
        
    Returns:
        Model: Attention seq2seq model
    """
     Encoder
    encoder_inputs = Input(shape=(None,))
    encoder_embedding = Embedding(vocab_size, embedding_dim)(encoder_inputs)
    encoder_lstm = LSTM(latent_dim, return_sequences=True, return_state=True)
    encoder_outputs, state_h, state_c = encoder_lstm(encoder_embedding)
    encoder_states = [state_h, state_c]
    
     Decoder
    decoder_inputs = Input(shape=(None,))
    decoder_embedding = Embedding(vocab_size, embedding_dim)(decoder_inputs)
    decoder_lstm = LSTM(latent_dim, return_sequences=True, return_state=True)
    decoder_outputs, _, _ = decoder_lstm(decoder_embedding, initial_state=encoder_states)
    
     Attention
    attention = Attention()([decoder_outputs, encoder_outputs])
    
     Concatenate attention with decoder outputs
    decoder_concat_input = Concatenate(axis=-1)([decoder_outputs, attention])
    
     Output
    decoder_dense = Dense(vocab_size, activation='softmax')
    decoder_outputs = decoder_dense(decoder_concat_input)
    
    model = Model([encoder_inputs, decoder_inputs], decoder_outputs)
    
    return model

 Build attention model
attention_model = build_attention_seq2seq(vocab_size, embedding_dim, latent_dim)
attention_model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])

print("Attention Seq2Seq Model:")
attention_model.summary()
```

---

 Task 4: Inference with Beam Search
Implement beam search for better translations.

```python
import heapq

def beam_search_decode(encoder_model, decoder_model, input_seq, 
                       target_tokenizer, beam_width=3, max_length=50):
    """
    Decode sequence using beam search.
    
    Args:
        encoder_model: Encoder model
        decoder_model: Decoder model
        input_seq: Input sequence
        target_tokenizer: Target tokenizer
        beam_width (int): Beam width
        max_length (int): Maximum sequence length
        
    Returns:
        str: Best translation
    """
     Encode input
    states_value = encoder_model.predict(input_seq)
    
     Start with empty sequence
    start_token = 1   Assuming 1 is START token
    sequences = [[[], 0.0, states_value]]
    
    for _ in range(max_length):
        all_candidates = []
        
        for seq, score, states in sequences:
            if len(seq) > 0 and seq[-1] == 2:   END token
                all_candidates.append((seq, score, states))
                continue
            
             Prepare decoder input
            target_seq = np.zeros((1, 1))
            target_seq[0, 0] = seq[-1] if seq else start_token
            
             Predict
            output_tokens, h, c = decoder_model.predict([target_seq] + states)
            
             Get top k predictions
            top_k_indices = np.argsort(output_tokens[0, -1, :])[-beam_width:]
            
            for idx in top_k_indices:
                candidate_seq = seq + [idx]
                candidate_score = score - np.log(output_tokens[0, -1, idx])
                candidate_states = [h, c]
                all_candidates.append((candidate_seq, candidate_score, candidate_states))
        
         Select top beam_width sequences
        ordered = sorted(all_candidates, key=lambda x: x[1])
        sequences = ordered[:beam_width]
        
         Check if all sequences are complete
        if all(seq[-1] == 2 for seq, _, _ in sequences):
            break
    
     Return best sequence
    best_seq = sequences[0][0]
    
     Decode to text
    reverse_word_map = dict(map(reversed, target_tokenizer.word_index.items()))
    words = [reverse_word_map.get(idx, '') for idx in best_seq if idx > 2]
    
    return ' '.join(words)

 Note: This requires trained models to work
print("Beam search implementation ready")
print("Train models first, then use for inference")
```

---

 Task 5: BLEU Score Evaluation
Evaluate translation quality with BLEU.

```python
from collections import Counter
import math

def calculate_bleu(reference, candidate, n=4):
    """
    Calculate BLEU score.
    
    Args:
        reference (str): Reference translation
        candidate (str): Candidate translation
        n (int): Maximum n-gram size
        
    Returns:
        float: BLEU score
    """
    reference_tokens = reference.split()
    candidate_tokens = candidate.split()
    
     Brevity penalty
    bp = 1.0
    if len(candidate_tokens) < len(reference_tokens):
        bp = math.exp(1 - len(reference_tokens) / len(candidate_tokens))
    
     Calculate n-gram precisions
    precisions = []
    for i in range(1, n + 1):
        ref_ngrams = Counter([tuple(reference_tokens[j:j+i]) for j in range(len(reference_tokens)-i+1)])
        cand_ngrams = Counter([tuple(candidate_tokens[j:j+i]) for j in range(len(candidate_tokens)-i+1)])
        
        matches = sum((cand_ngrams & ref_ngrams).values())
        total = max(len(candidate_tokens) - i + 1, 1)
        
        precisions.append(matches / total if total > 0 else 0)
    
     Geometric mean of precisions
    if all(p > 0 for p in precisions):
        geo_mean = math.exp(sum(math.log(p) for p in precisions) / n)
    else:
        geo_mean = 0
    
    return bp  geo_mean

 Test
reference = "the cat is on the mat"
candidate1 = "the cat is on the mat"
candidate2 = "the cat is sitting on the mat"
candidate3 = "dog is under table"

print("BLEU Scores:")
print(f"Perfect: {calculate_bleu(reference, candidate1):.4f}")
print(f"Good: {calculate_bleu(reference, candidate2):.4f}")
print(f"Poor: {calculate_bleu(reference, candidate3):.4f}")
```

---

 ✅ Completion Checklist

- [ ] Encoder-decoder architecture built
- [ ] Data preparation working
- [ ] Attention mechanism implemented
- [ ] Beam search decoding created
- [ ] BLEU evaluation implemented
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create complete translation pipeline:

```python
class TranslationPipeline:
    """
    Complete machine translation pipeline.
    """
    def __init__(self, source_lang, target_lang):
        """
        Initialize pipeline.
        
        Args:
            source_lang (str): Source language code
            target_lang (str): Target language code
        """
         Your implementation
        pass
    
    def train(self, parallel_corpus):
        """
        Train translation model.
        
        Args:
            parallel_corpus (list): List of (source, target) pairs
        """
         Your implementation
        pass
    
    def translate(self, text, method='greedy'):
        """
        Translate text.
        
        Args:
            text (str): Source text
            method (str): 'greedy' or 'beam'
            
        Returns:
            str: Translated text
        """
         Your implementation
        pass
    
    def evaluate(self, test_pairs):
        """
        Evaluate on test set.
        
        Args:
            test_pairs (list): List of (source, reference) pairs
            
        Returns:
            dict: Evaluation metrics
        """
         Your implementation
        pass
```
