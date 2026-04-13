 Week 4 Exercises: Advanced Optimization & Ensembles

 Objectives
Master the art of "squeezing" every bit of performance out of your models while maintaining reproducibility.

 1. The Tuning Marathon
- Use `RandomizedSearchCV` to explore a wide range of hyperparameters for an `XGBoost` or `GradientBoosting` model.
- Take the top 3 configurations and use `GridSearchCV` to perform a "micro-search" around those values.
- Compare the time taken vs. the improvement in accuracy.

 2. Regularization Showdown
- Load a high-dimensional dataset (many features).
- Train a Lasso (L1) model. 
- Identify which features had their coefficients shrunk to zero.
- Train an Elastic Net model and compare its performance to Lasso and Ridge.
- Goal: Find the best balance between feature selection and predictive power.

 3. Ensemble Playground
- Voting Classifier: Create an ensemble using a Logistic Regression, a Decision Tree, and a KNN. Compare "Hard" voting vs. "Soft" voting.
- Stacking: Use the same three models as base learners and a `RandomForest` as the final meta-model.
- Does the stacked model outperform the best individual model?

 4. Experiment Tracking (Light)
- Create a dictionary to manually log your experiments.
- Track: `Model Type`, `Alpha`, `N_Estimators`, `Train Score`, `Test Score`.
- Identify which parameter change had the most significant impact on the final metric.

 5. Deployment Readiness
- Create a final, optimized `Pipeline` including your best tuned hyperparameters.
- Export the model and verify that the file size is manageable.

---
