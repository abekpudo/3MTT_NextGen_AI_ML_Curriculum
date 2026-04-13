 Week 2 Assignment: Comparative Modeling and Production Pipelines

 Objectives
- Implement and compare multiple supervised learning models.
- Use advanced evaluation techniques to justify model selection.
- Deploy a production-ready Scikit-Learn Pipeline.

 Assignment Tasks
1. Model Selection & Training:
	- Using the cleaned data from Week 1, train at least two different types of models (e.g., Logistic Regression vs. Random Forest).
	- Use K-Fold Cross-Validation to compare their baseline performances.

2. Hyperparameter Tuning:
	- Use `GridSearchCV` or `RandomizedSearchCV` to find the optimal parameters for your best-performing model.
	- Document which parameters were tuned and how they affected the score.

3. In-Depth Evaluation:
	- Provide a Classification Report or Regression Metrics Summary.
	- If classification: Plot the Confusion Matrix and the ROC-AUC Curve.
	- If regression: Plot Predicted vs. Actual and analyze the residuals.
	- Write a one-paragraph justification: Why is this model the best choice for this specific dataset?

4. The "Production" Pipeline:
	- Wrap your final preprocessor and tuned model into a single `Pipeline`.
	- Serialize the pipeline to a `.pkl` file using `joblib`.
	- Demonstrate that the pipeline works on a raw, unseen test sample.

5. Submission Requirements:
	- A Jupyter Notebook (`.ipynb`) with structured sections and comments.
	- The saved model file (`model.pkl`).
	- A one-page "Executive Summary" PDF explaining your model's performance and limitations.

 Evaluation Criteria
| Item | Weight |
|------|--------|
| Technical Correctness (CV, GridSearchCV, Pipeline) | 35% |
| Evaluation Depth & Visualization | 25% |
| Code Quality & Documentation | 20% |
| Model Serialization & Reproducibility | 20% |

---
