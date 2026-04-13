 Week 3 Assignment: The Optimization Challenge

 Objectives
- Apply advanced tuning and regularization to a complex dataset.
- Build and evaluate a state-of-the-art ensemble model.
- Demonstrate systematic experiment tracking and reproducibility.

 Assignment Tasks
1. The Base Model:
	- Choose a complex dataset (e.g., Tabular data with at least 20 features).
	- Establish a baseline score using a simple `DecisionTree` or `LogisticRegression`.

2. The Tuning Pipeline:
	- Implement a `RandomizedSearchCV` to tune an `XGBoost`, `LightGBM`, or `GradientBoosting` model.
	- Document the search space and why you chose those specific ranges.

3. Regularization & Stability:
	- Apply `ElasticNet` or `Dropout` (if using boosting) to prevent overfitting.
	- Compare the training vs. validation curves to prove that the model is generalizing.

4. The "Master" Ensemble:
	- Create a `StackingClassifier` or `StackingRegressor`.
	- Combine at least 3 different algorithms as base learners.
	- Finalize the model and report the final improvement over the baseline.

5. Experiment Logging:
	- Create an "Experiment Log" (CSV or simple Markdown table).
	- Log at least 5 different model versions with their key hyperparameters and final test scores.

 Submission Requirements
- A Jupyter Notebook (`.ipynb`) containing the full workflow.
- An "Experiment Log" table.
- A final "Recommendation" PDF: If this model were to be used in production tomorrow, what are the top 3 risks we should monitor?

 Evaluation Criteria
| Item | Weight |
|------|--------|
| Complexity of Tuning & Search Strategy | 30% |
| Ensemble Implementation & Results | 30% |
| Experiment Tracking & Reproducibility | 20% |
| Risk Analysis & Recommendations | 20% |

---
