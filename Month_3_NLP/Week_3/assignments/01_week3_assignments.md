 Week 3 Assignment: Market Sentiment & Topic Discovery

 Objectives
- Apply unsupervised learning to discover themes in large text data.
- Perform a "Hybrid" sentiment analysis (Lexicon + ML).
- Create a data-driven report based on NLP insights.

 Assignment Tasks
1. Dataset Acquisition:
	- Scrape or download a dataset of Customer Reviews or News Articles related to a specific industry (e.g., Nigerian FinTech, e-commerce, or sports).
	- Ensure the dataset has at least 500 rows.

2. Topic Discovery (Theme Mapping):
	- Apply LDA or NMF to the dataset.
	- Identify 5 distinct topics.
	- For each topic, provide:
		- Top 10 Keywords.
		- A human-readable title (e.g., "Customer Service Issues," "Product Reliability").
		- The percentage of the total dataset that belongs to this topic.

3. Hybrid Sentiment Analysis:
	- Use VADER to get an initial sentiment score for each document.
	- Use your BERT fine-tuned model (from Week 2) to predict the sentiment labels.
	- Compare the results: Where did VADER and BERT disagree?

4. Visualizing the Insights:
	- Create a Topic-Sentiment Heatmap: Which topics are the most negative? Which are the most positive?
	- Generate a Word Cloud for the most "negative" topic to identify common pain points.

5. Submission Requirements:
	- A Jupyter Notebook (`.ipynb`).
	- A "Stakeholder Report" PDF: If you were the CEO of this company, what 3 actions would you take based on these NLP findings?

 Evaluation Criteria
| Item | Weight |
|------|--------|
| Topic Modeling Coherence & Interpretation | 30% |
| Hybrid Sentiment Comparison (VADER vs BERT) | 25% |
| Visualization Quality & Insight Generation | 25% |
| Practical Business Recommendations | 20% |

---
