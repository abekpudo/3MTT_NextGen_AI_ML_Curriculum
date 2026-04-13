 Week 2 Exercises: Embeddings, BERT, and Semantic Search

 Objectives
Transition from traditional word counts to dense semantic vectors and learn to fine-tune state-of-the-art Transformer models.

 1. Static Embedding Exploration (Word2Vec)
- Use `gensim` to load the `glove-twitter-25` model.
- Find the top 5 words most similar to "nigeria" and "coding".
- Analogy Test: Try to solve the analogy: `Man is to King as Woman is to [?]` using the `most_similar` function.
- OOV Check: Try to find the vector for a made-up word (e.g., "ai-bot-3mtt"). What happens?

 2. Contextual Similarity with Sentence-Transformers
- Load the `all-MiniLM-L6-v2` model.
- Take three sentences:
    1. "I am going to the bank to deposit money."
    2. "I sat on the river bank to fish."
    3. "The financial institution is closed."
- Calculate the cosine similarity between (1 and 2) and (1 and 3). 
- Analysis: Does the model understand that "bank" in sentence 1 is more similar to sentence 3 than sentence 2?

 3. BERT Fine-Tuning Setup
- Use Hugging Face `Datasets` to load a small slice of the `imdb` dataset.
- Initialize an `AutoTokenizer` for `distilbert-base-uncased`.
- Write a function to tokenize the data with a `max_length` of 128 and `padding="max_length"`.
- Print the `input_ids` and `attention_mask` for the first record.

 4. Building a Semantic Search Index
- Create a list of 10 sentences about different topics (Food, Tech, Travel).
- Use `SentenceTransformer` to encode them.
- Build a `faiss.IndexFlatL2` and add the embeddings.
- Search for "What is the best place to visit?" and "How to cook jollof rice?". 
- Are the results relevant?

 5. Reflection
- Briefly explain the difference between a Bi-Encoder and a Cross-Encoder in your own words.

---
