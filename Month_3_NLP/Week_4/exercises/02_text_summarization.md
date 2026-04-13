 Exercise 2: Text Summarization Systems

 🎯 Objective
Build extractive and abstractive text summarization systems.

---

 📋 Tasks

 Task 1: TextRank Extractive Summarization
Implement PageRank-based extractive summarization.

```python
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import networkx as nx

class TextRankSummarizer:
    """
    TextRank-based extractive summarization.
    """
    def __init__(self):
        """Initialize summarizer."""
        self.vectorizer = TfidfVectorizer()
    
    def summarize(self, text, ratio=0.2):
        """
        Generate extractive summary.
        
        Args:
            text (str): Input text
            ratio (float): Summary ratio (0-1)
            
        Returns:
            str: Summary
        """
         Split into sentences
        sentences = self._split_sentences(text)
        
        if len(sentences) < 3:
            return text
        
         Vectorize sentences
        sentence_vectors = self.vectorizer.fit_transform(sentences)
        
         Create similarity matrix
        similarity_matrix = cosine_similarity(sentence_vectors)
        
         Build graph and apply PageRank
        nx_graph = nx.from_numpy_array(similarity_matrix)
        scores = nx.pagerank(nx_graph)
        
         Rank sentences
        ranked_sentences = sorted(((scores[i], s) for i, s in enumerate(sentences)), reverse=True)
        
         Select top sentences
        num_sentences = max(1, int(len(sentences)  ratio))
        selected_sentences = [s for _, s in ranked_sentences[:num_sentences]]
        
         Maintain original order
        selected_indices = [sentences.index(s) for s in selected_sentences]
        selected_indices.sort()
        
        summary = ' '.join([sentences[i] for i in selected_indices])
        
        return summary
    
    def _split_sentences(self, text):
        """Split text into sentences."""
        import re
        sentences = re.split(r'(?<=[.!?])\s+', text)
        return [s.strip() for s in sentences if s.strip()]

 Test
text = """
Natural language processing (NLP) is a subfield of linguistics, computer science, and artificial intelligence.
It concerns the interactions between computers and human language.
NLP enables computers to process and analyze large amounts of natural language data.
The technology can understand the contextual nuances of language within documents.
Machine learning is crucial for modern NLP applications.
Deep learning has revolutionized NLP in recent years.
Transformer models like BERT have achieved state-of-the-art results.
These models use attention mechanisms to capture long-range dependencies.
"""

summarizer = TextRankSummarizer()
summary = summarizer.summarize(text, ratio=0.4)

print(f"Original length: {len(text)} chars")
print(f"Summary length: {len(summary)} chars")
print(f"\nSummary:\n{summary}")
```

---

 Task 2: Frequency-Based Summarization
Implement frequency-based extractive summarization.

```python
from collections import Counter
import re

class FrequencySummarizer:
    """
    Frequency-based extractive summarization.
    """
    def __init__(self):
        """Initialize summarizer."""
        self.word_frequencies = None
    
    def _compute_frequencies(self, text):
        """
        Compute word frequencies.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Word frequencies
        """
        words = re.findall(r'\w+', text.lower())
        frequencies = Counter(words)
        
         Normalize
        max_freq = max(frequencies.values())
        for word in frequencies:
            frequencies[word] /= max_freq
        
        return frequencies
    
    def score_sentence(self, sentence, frequencies):
        """
        Score sentence based on word frequencies.
        
        Args:
            sentence (str): Input sentence
            frequencies (dict): Word frequencies
            
        Returns:
            float: Sentence score
        """
        words = re.findall(r'\w+', sentence.lower())
        
        if not words:
            return 0
        
        score = sum(frequencies.get(word, 0) for word in words) / len(words)
        return score
    
    def summarize(self, text, num_sentences=3):
        """
        Generate summary.
        
        Args:
            text (str): Input text
            num_sentences (int): Number of sentences in summary
            
        Returns:
            str: Summary
        """
         Compute frequencies
        frequencies = self._compute_frequencies(text)
        
         Split into sentences
        sentences = re.split(r'(?<=[.!?])\s+', text)
        sentences = [s.strip() for s in sentences if s.strip()]
        
         Score sentences
        sentence_scores = [(i, self.score_sentence(s, frequencies), s) 
                          for i, s in enumerate(sentences)]
        
         Select top sentences
        sentence_scores.sort(key=lambda x: x[1], reverse=True)
        top_sentences = sentence_scores[:num_sentences]
        
         Maintain original order
        top_sentences.sort(key=lambda x: x[0])
        
        summary = ' '.join([s for _, _, s in top_sentences])
        
        return summary

 Test
summarizer2 = FrequencySummarizer()
summary2 = summarizer2.summarize(text, num_sentences=2)

print("\nFrequency-Based Summary:")
print(summary2)
```

---

 Task 3: BART Abstractive Summarization
Use BART for abstractive summarization.

```python
from transformers import BartTokenizer, BartForConditionalGeneration

class BARTSummarizer:
    """
    BART-based abstractive summarization.
    """
    def __init__(self, model_name='facebook/bart-large-cnn'):
        """
        Initialize BART summarizer.
        
        Args:
            model_name (str): BART model name
        """
        self.tokenizer = BartTokenizer.from_pretrained(model_name)
        self.model = BartForConditionalGeneration.from_pretrained(model_name)
    
    def summarize(self, text, max_length=150, min_length=30, num_beams=4):
        """
        Generate abstractive summary.
        
        Args:
            text (str): Input text
            max_length (int): Maximum summary length
            min_length (int): Minimum summary length
            num_beams (int): Number of beams for beam search
            
        Returns:
            str: Summary
        """
         Tokenize
        inputs = self.tokenizer([text], max_length=1024, return_tensors='pt', truncation=True)
        
         Generate
        summary_ids = self.model.generate(
            inputs['input_ids'],
            max_length=max_length,
            min_length=min_length,
            length_penalty=2.0,
            num_beams=num_beams,
            early_stopping=True
        )
        
         Decode
        summary = self.tokenizer.decode(summary_ids[0], skip_special_tokens=True)
        
        return summary

 Test
bart_summarizer = BARTSummarizer()

long_article = """
Artificial intelligence (AI) is intelligence demonstrated by machines, in contrast to the natural 
intelligence displayed by humans and animals. Leading AI textbooks define the field as the study 
of "intelligent agents": any device that perceives its environment and takes actions that maximize 
its chance of successfully achieving its goals. Colloquially, the term "artificial intelligence" 
is often used to describe machines that mimic "cognitive" functions that humans associate with 
the human mind, such as "learning" and "problem solving".

As machines become increasingly capable, tasks considered to require "intelligence" are often 
removed from the definition of AI, a phenomenon known as the AI effect. A quip in Tesler's Theorem 
says "AI is whatever hasn't been done yet." For instance, optical character recognition is 
frequently excluded from things considered to be AI, having become a routine technology. Modern 
machine capabilities generally classified as AI include successfully understanding human speech, 
competing at the highest level in strategic game systems, autonomously operating cars, intelligent 
routing in content delivery networks, and military simulations.
"""

summary_bart = bart_summarizer.summarize(long_article)
print("BART Summary:")
print(summary_bart)
```

---

 Task 4: ROUGE Evaluation
Evaluate summaries with ROUGE metrics.

```python
from collections import Counter

def rouge_n(reference, candidate, n=1):
    """
    Calculate ROUGE-N score.
    
    Args:
        reference (str): Reference summary
        candidate (str): Candidate summary
        n (int): N-gram size
        
    Returns:
        dict: ROUGE-N metrics
    """
    def get_ngrams(text, n):
        words = text.split()
        return [tuple(words[i:i+n]) for i in range(len(words)-n+1)]
    
    ref_ngrams = Counter(get_ngrams(reference, n))
    cand_ngrams = Counter(get_ngrams(candidate, n))
    
    overlap = sum((ref_ngrams & cand_ngrams).values())
    
    recall = overlap / sum(ref_ngrams.values()) if ref_ngrams else 0
    precision = overlap / sum(cand_ngrams.values()) if cand_ngrams else 0
    f1 = 2  precision  recall / (precision + recall) if (precision + recall) > 0 else 0
    
    return {
        'recall': recall,
        'precision': precision,
        'f1': f1
    }

def rouge_l(reference, candidate):
    """
    Calculate ROUGE-L (Longest Common Subsequence).
    
    Args:
        reference (str): Reference summary
        candidate (str): Candidate summary
        
    Returns:
        dict: ROUGE-L metrics
    """
    ref_words = reference.split()
    cand_words = candidate.split()
    
     LCS length
    m, n = len(ref_words), len(cand_words)
    dp = [[0]  (n+1) for _ in range(m+1)]
    
    for i in range(1, m+1):
        for j in range(1, n+1):
            if ref_words[i-1] == cand_words[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    lcs_length = dp[m][n]
    
    recall = lcs_length / len(ref_words) if ref_words else 0
    precision = lcs_length / len(cand_words) if cand_words else 0
    f1 = 2  precision  recall / (precision + recall) if (precision + recall) > 0 else 0
    
    return {
        'recall': recall,
        'precision': precision,
        'f1': f1
    }

def evaluate_summary(reference, candidate):
    """
    Evaluate summary with multiple ROUGE metrics.
    
    Args:
        reference (str): Reference summary
        candidate (str): Candidate summary
        
    Returns:
        dict: All ROUGE metrics
    """
    return {
        'rouge_1': rouge_n(reference, candidate, n=1),
        'rouge_2': rouge_n(reference, candidate, n=2),
        'rouge_l': rouge_l(reference, candidate)
    }

 Test
reference = "natural language processing enables computers to understand human language"
candidate = "computers can understand language through natural language processing"

metrics = evaluate_summary(reference, candidate)
print("\nROUGE Scores:")
for metric, scores in metrics.items():
    print(f"{metric}: R={scores['recall']:.3f}, P={scores['precision']:.3f}, F1={scores['f1']:.3f}")
```

---

 Task 5: Multi-Document Summarization
Summarize multiple documents.

```python
class MultiDocumentSummarizer:
    """
    Summarize multiple documents into one summary.
    """
    def __init__(self, method='textrank'):
        """
        Initialize summarizer.
        
        Args:
            method (str): 'textrank' or 'frequency'
        """
        self.method = method
        if method == 'textrank':
            self.summarizer = TextRankSummarizer()
        else:
            self.summarizer = FrequencySummarizer()
    
    def summarize(self, documents, summary_length='short'):
        """
        Summarize multiple documents.
        
        Args:
            documents (list): List of documents
            summary_length (str): 'short', 'medium', or 'long'
            
        Returns:
            str: Combined summary
        """
         Combine all documents
        combined_text = ' '.join(documents)
        
         Determine ratio based on length
        ratios = {'short': 0.1, 'medium': 0.2, 'long': 0.3}
        ratio = ratios.get(summary_length, 0.2)
        
         Generate summary
        if self.method == 'textrank':
            summary = self.summarizer.summarize(combined_text, ratio=ratio)
        else:
            num_sentences = max(1, int(len(combined_text.split('. '))  ratio))
            summary = self.summarizer.summarize(combined_text, num_sentences=num_sentences)
        
        return summary
    
    def summarize_clustered(self, documents, num_clusters=3):
        """
        Cluster documents and summarize each cluster.
        
        Args:
            documents (list): List of documents
            num_clusters (int): Number of clusters
            
        Returns:
            list: List of cluster summaries
        """
        from sklearn.feature_extraction.text import TfidfVectorizer
        from sklearn.cluster import KMeans
        
         Vectorize documents
        vectorizer = TfidfVectorizer()
        doc_vectors = vectorizer.fit_transform(documents)
        
         Cluster
        kmeans = KMeans(n_clusters=num_clusters, random_state=42)
        clusters = kmeans.fit_predict(doc_vectors)
        
         Summarize each cluster
        summaries = []
        for i in range(num_clusters):
            cluster_docs = [documents[j] for j in range(len(documents)) if clusters[j] == i]
            if cluster_docs:
                cluster_summary = self.summarize(cluster_docs, summary_length='short')
                summaries.append({
                    'cluster_id': i,
                    'num_docs': len(cluster_docs),
                    'summary': cluster_summary
                })
        
        return summaries

 Test
docs = [
    "AI is transforming healthcare with predictive diagnostics.",
    "Machine learning helps doctors detect diseases early.",
    "Neural networks improve medical imaging analysis.",
    "AI applications in finance include fraud detection.",
    "Algorithmic trading uses machine learning models.",
    "Banks deploy AI for risk assessment.",
]

multi_summarizer = MultiDocumentSummarizer()
combined_summary = multi_summarizer.summarize(docs, summary_length='medium')

print("\nMulti-Document Summary:")
print(combined_summary)

clustered_summary = multi_summarizer.summarize_clustered(docs, num_clusters=2)
print("\nClustered Summaries:")
for item in clustered_summary:
    print(f"\nCluster {item['cluster_id']} ({item['num_docs']} docs):")
    print(item['summary'])
```

---

 ✅ Completion Checklist

- [ ] TextRank summarization working
- [ ] Frequency-based summarization implemented
- [ ] BART abstractive summarization used
- [ ] ROUGE metrics calculated
- [ ] Multi-document summarization complete
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create complete summarization system:

```python
class TextSummarizationPipeline:
    """
    Complete text summarization pipeline.
    """
    def __init__(self):
        """Initialize pipeline."""
        self.extractive_methods = {
            'textrank': TextRankSummarizer(),
            'frequency': FrequencySummarizer()
        }
        self.abstractive_methods = {
            'bart': BARTSummarizer()
        }
    
    def summarize(self, text, method='textrank', length='medium'):
        """
        Summarize text using specified method.
        
        Args:
            text (str): Input text
            method (str): Summarization method
            length (str): Summary length
            
        Returns:
            str: Summary
        """
         Your implementation
        pass
    
    def compare_methods(self, text):
        """
        Compare different summarization methods.
        
        Args:
            text (str): Input text
            
        Returns:
            dict: Comparison results
        """
         Your implementation
        pass
    
    def evaluate(self, reference, candidate):
        """
        Evaluate summary quality.
        
        Args:
            reference (str): Reference summary
            candidate (str): Candidate summary
            
        Returns:
            dict: Evaluation metrics
        """
         Your implementation
        pass
```
