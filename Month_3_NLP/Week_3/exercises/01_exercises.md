 Week 3 Exercises: Sentiment, Topics, and Advanced Metrics

 Objectives
Implement specialized NLP workflows including rule-based sentiment, unsupervised topic discovery, and rigorous error analysis.

 1. Rule-Based Sentiment Analysis
- Install and use VADER (`nltk.sentiment.vader`).
- Take a list of 5 sentences, including some with capitalization and punctuation (e.g., "I LOVE this!", "I love this.").
- Compare: How does VADER handle the intensity of sentiment based on the punctuation?
- Try a sarcastic sentence. Does VADER catch it?

 2. Unsupervised Topic Modeling (LDA)
- Use a dataset of news headlines or short descriptions.
- Preprocess the text (Tokenize, Remove Stopwords, Lemmatize).
- Train an LDA model with `n_components=3`.
- Print the top 10 words for each topic.
- Goal: Give each of the 3 topics a human-readable name based on the keywords.

 3. Multi-Class Classification Lab
- Load a dataset with more than 2 labels (e.g., Categorizing emails into "Billing," "Tech Support," and "General").
- Train a `NaiveBayes` classifier.
- Generate a `classification_report`.
- Identify: Which class has the lowest Recall? What does this mean for the user?

 4. Error Analysis with Heatmaps
- Plot a Confusion Matrix for your Multi-Class model.
- Identify the most common "misclassification pair" (e.g., model keeps thinking "Billing" is "General").
- Look at 3 actual examples of this mistake and explain why the model got it wrong.

 5. Metric Challenge
- Calculate the F1-Score manually for a model that has:
    - True Positives: 80
    - False Positives: 10
    - False Negatives: 30
- Compare your manual calculation with the Scikit-Learn result.

---
