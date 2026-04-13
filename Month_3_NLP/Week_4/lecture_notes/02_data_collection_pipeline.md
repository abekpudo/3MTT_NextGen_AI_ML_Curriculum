 Data Collection & Labeling for NLP

 1. Overview
In NLP, your "Code" is often less important than your "Corpus." This lecture covers how to build a pipeline that automatically sources, cleans, and labels text data for your Capstone project.

 2. Sourcing Text Data

 2.1. Web Scraping (The Raw Method)
Useful for news, blogs, or forums.
- Tools: `BeautifulSoup`, `Scrapy`, `Selenium`.
- Best Practice: Always check `robots.txt` before scraping.

 2.2. APIs (The Official Method)
- Examples: Twitter API, NewsAPI, Reddit API (PRAW).
- Advantage: Data is already semi-structured (JSON).

 2.3. Public Repositories (The Efficient Method)
- Hugging Face Datasets: Thousands of ready-to-use datasets.
- Kaggle: Excellent for competition-grade text data.

 3. The Annotation Process
If your data isn't labeled (e.g., you scraped 1,000 tweets but don't know if they are positive or negative), you need an annotation strategy.
- Manual Labeling: You (or a team) label the data using tools like Label Studio or Doccano.
- Weak Supervision: Using rules or keywords to auto-label (e.g., "If it contains 'hate', label as Negative").
- LLM-Labeling: Using GPT-4 or Claude to label your small dataset, which you then use to train a smaller, faster model.

 4. Hands-On: Loading Data with Hugging Face
```python
from datasets import load_dataset

 1. Load a public dataset
dataset = load_dataset("imdb", split="train[:1000]")  Only first 1000 rows

 2. Access a sample
print(dataset[0]['text'])
print(dataset[0]['label'])

 3. Save to disk for offline use
dataset.save_to_disk("./my_local_data")
```

 5. Data Versioning with DVC
Since text data can be large, don't store it directly in Git. Use DVC (Data Version Control) to track changes to your data files while keeping your Git repo light.

 6. Resources
- [BeautifulSoup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Hugging Face Datasets Hub](https://huggingface.co/datasets)
- [Label Studio: Open Source Data Labeling](https://labelstud.io/)

---
