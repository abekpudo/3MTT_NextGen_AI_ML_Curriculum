 Week 3 Exercises: Regression, Classification, and Pipelines

 Objectives
Build, evaluate, and bundle machine learning models into reproducible pipelines.

 1. Regression Lab
- Load a regression dataset (e.g., `Housing Prices` or `Diabetes` from Scikit-Learn).
- Train a Linear Regression model and print the coefficients for each feature.
- Train a Ridge and Lasso model on the same data.
- Compare the RMSE and R2 Score for all three models.
- Bonus: Visualize the residuals of your best model using a scatter plot.

 2. Classification Lab
- Load a classification dataset (e.g., `Iris` or `Wine`).
- Train a Logistic Regression and a Random Forest classifier.
- Generate a Confusion Matrix for both.
- Compare the Precision, Recall, and F1-Score.
- Insight: Which model was better at avoiding False Positives?

 3. Pipeline Construction
- Create a `ColumnTransformer` that:
    - Scales numerical features with `StandardScaler`.
    - Encodes categorical features with `OneHotEncoder`.
- Wrap this preprocessor and a `RandomForestClassifier` into a single `Pipeline`.
- Train the pipeline on your raw training data (before any manual preprocessing).
- Use the pipeline to make a prediction on a single new "raw" data point.

 4. Model Evaluation & Cross-Validation
- Use `cross_val_score` with 5 folds to evaluate your pipeline's stability.
- Plot an ROC Curve for your classification model.
- Identify if your model is Overfitting or Underfitting based on the difference between training and validation scores.

 5. Serialization
- Save your trained pipeline using `joblib`.
- Restart your Python kernel (or open a new script), load the pipeline, and verify it still works by making a prediction.

---
