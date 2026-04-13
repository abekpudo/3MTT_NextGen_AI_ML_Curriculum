 Week 2 Assignment: Semantic Search Engine OR Fine-Tuned Classifier

 Objectives
- Implement an end-to-end modern NLP solution using Transformers.
- Demonstrate mastery of either vector-based retrieval or deep transfer learning.

 Choose ONE of the following options:

 Option A: The "Intelligent" Search Engine
1. Dataset: Find a collection of documents (e.g., FAQ list, news articles, or 100+ Kaggle descriptions).
2. Implementation:
    - Use `sentence-transformers` to create embeddings for all documents.
    - Build a searchable index using FAISS.
    - Implement a "Search Function" that takes a query and returns the Top 3 results.
3. Advanced Step: Implement a simple Cross-Encoder re-ranker to improve the results of your Top 3.
4. Analysis: Compare the results of your semantic search with a basic "Keyword" search.

 Option B: The "State-of-the-Art" BERT Classifier
1. Dataset: Choose a text classification dataset (e.g., Sentiment, Emotion, or Spam).
2. Implementation:
    - Fine-tune a `DistilBERT` or `BERT` model using the Hugging Face `Trainer` API.
    - Track your training loss and validation accuracy per epoch.
3. Evaluation: 
    - Generate a Confusion Matrix.
    - Calculate F1-Score and ROC-AUC.
4. Deployment Preparation: Serialize the model and tokenizer so they can be loaded for inference later.

 Submission Requirements
- A Jupyter Notebook (`.ipynb`) with commented code.
- A brief "Result Summary" PDF showing example outputs and performance metrics.

 Evaluation Criteria
| Item | Weight |
|------|--------|
| Correct Use of Transformer Library | 35% |
| Retrieval Accuracy / Model Performance | 25% |
| Code Efficiency & Documentation | 20% |
| Comparative Analysis (Semantic vs Keyword or Baseline) | 20% |

---
