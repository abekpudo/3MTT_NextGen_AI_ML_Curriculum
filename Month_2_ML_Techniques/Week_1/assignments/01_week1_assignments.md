 Week 1 Assignment: End-to-End Data Preprocessing Pipeline

 Objectives
- Demonstrate proficiency in loading, exploring, and cleaning a dataset.
- Implement a feature engineering workflow using Scikit-Learn.
- Prepare data for modeling by correctly splitting and validating feature distributions.

 Assignment Tasks
1. Data Acquisition & Profiling:
	- Select a dataset from Kaggle or UCI Machine Learning Repository (e.g., House Prices, Heart Disease, or Telecom Churn).
	- Perform a complete EDA. Include at least 4 visualizations (Distribution, Correlation, Box-plot, and Scatter-plot).
	- Summarize the "messiness" of the data (missing values, types, outliers).

2. The Cleaning Pipeline:
	- Handle all missing values using appropriate strategies (mean for normal distributions, median for skewed, mode for categories).
	- Standardize at least two text-based columns.
	- Document why you chose to drop or keep specific outliers.

3. Engineering Features:
	- Encode all categorical variables.
	- Create at least one interaction feature or a calculated ratio.
	- Scale numerical features that will be used in the model.

4. Model Readiness:
	- Split the data into training (80%) and test (20%) sets.
	- If doing classification, use stratified sampling.
	- Export the cleaned `X_train.csv` and `y_train.csv` for use in next week's modeling lecture.

5. Submission Requirements:
	- A Jupyter Notebook (`.ipynb`) with commented code.
	- A short PDF report (1-2 pages) summarizing your EDA findings and cleaning decisions.

 Evaluation Criteria
| Item | Weight |
|------|--------|
| EDA Depth & Visualization Quality | 30% |
| Appropriateness of Cleaning Strategies | 25% |
| Feature Engineering Creativity & Correctness | 25% |
| Documentation & Reasoning | 20% |

---
