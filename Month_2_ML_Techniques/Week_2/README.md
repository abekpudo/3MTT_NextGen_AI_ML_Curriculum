 Week 2: Core ML Algorithms & Model Evaluation

Duration: 5 days | Theme: Supervised Learning | Difficulty: Intermediate  
Prerequisite: Week 1 (Data Preparation), Python programming, Statistics basics

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Implement comprehensive regression models with diagnostics
- ✅ Build and tune classification algorithms (Tree-based, Linear, Instance-based)
- ✅ Handle imbalanced datasets with SMOTE and cost-sensitive learning
- ✅ Evaluate models using statistical tests and cross-validation
- ✅ Create production-ready ML pipelines
- ✅ Compare models statistically and select optimal approaches
- ✅ Analyze feature importance and model interpretability

---

 📖 Detailed Daily Schedule

 Day 1: Regression Models
Lecture: [01_regression_models.md](./lecture_notes/01_regression_models.md)

Topics:
- Linear Regression with assumptions validation
- Polynomial and non-linear regression
- Regularized regression (Ridge, Lasso, Elastic Net)
- Residual diagnostics and model validation
- Robust regression techniques

Hands-On:
```python
from lecture_notes import ComprehensiveRegressionFramework

 Initialize framework
reg_model = ComprehensiveRegressionFramework(model_type='linear')

 Fit with diagnostics
reg_model.fit(X_train, y_train)

 Get comprehensive analysis
diagnostics = reg_model.get_diagnostics()
residual_plots = reg_model.plot_residual_diagnostics()
```

Practice:
- [ ] Implement linear regression with assumption checks
- [ ] Compare polynomial vs. spline regression
- [ ] Apply regularization (Ridge, Lasso)
- [ ] Perform residual analysis
- [ ] Handle heteroscedasticity

---

 Day 2: Classification Algorithms - Part 1
Lecture: [02_classification_algorithms.md](./lecture_notes/02_classification_algorithms.md)

Topics:
- Logistic Regression with probability calibration
- Decision Trees with pruning strategies
- Random Forests (bagging, feature importance)
- Handling imbalanced data (SMOTE, ADASYN, class weights)

Hands-On:
```python
from lecture_notes import ClassificationFramework

 Build classification pipeline
clf = ClassificationFramework(
    algorithms=['logistic', 'random_forest', 'decision_tree'],
    handle_imbalance=True,
    imbalance_method='smote'
)

 Train and compare
results = clf.fit_compare(X_train, y_train, X_test, y_test)
```

Practice:
- [ ] Train Logistic, Decision Tree, Random Forest
- [ ] Apply SMOTE for imbalance
- [ ] Calibrate probability predictions
- [ ] Visualize decision boundaries
- [ ] Analyze feature importance

---

 Day 3: Classification Algorithms - Part 2
Lecture: [02_classification_algorithms.md](./lecture_notes/02_classification_algorithms.md) (continued)

Topics:
- Support Vector Machines (linear, RBF, polynomial kernels)
- Naive Bayes (Gaussian, Multinomial)
- K-Nearest Neighbors
- AdaBoost and Gradient Boosting (XGBoost, LightGBM)
- Ensemble methods (Voting, Stacking)

Hands-On:
```python
from lecture_notes import ClassificationFramework

 Advanced algorithms
clf = ClassificationFramework(
    algorithms=['svm', 'xgboost', 'lightgbm', 'adaboost']
)

 Compare all models
comparison_results = clf.compare_all_models(X, y)
clf.plot_model_comparison()
```

Practice:
- [ ] Tune SVM kernels and hyperparameters
- [ ] Train XGBoost and LightGBM
- [ ] Create ensemble (Voting/Stacking)
- [ ] Compare boosting vs. bagging
- [ ] Analyze learning curves

---

 Day 4: Comprehensive Model Evaluation
Lecture: [03_model_evaluation.md](./lecture_notes/03_model_evaluation.md)

Topics:
- Cross-validation strategies (K-Fold, Stratified, Time Series)
- Multiple metrics (Accuracy, Precision, Recall, F1, AUC)
- Statistical significance testing (paired t-test, McNemar)
- Learning curves and overfitting detection
- Confusion matrices and ROC/PR curves

Hands-On:
```python
from lecture_notes import ComprehensiveModelEvaluator

evaluator = ComprehensiveModelEvaluator(
    models=[model1, model2, model3],
    model_names=['Logistic', 'Random Forest', 'XGBoost']
)

 Comprehensive evaluation
results = evaluator.compare_all_models(X_test, y_test)
evaluator.plot_comparison_dashboard()

 Statistical testing
significance = evaluator.statistical_testing(X_test, y_test)
```

Practice:
- [ ] Implement nested cross-validation
- [ ] Generate ROC and PR curves
- [ ] Perform statistical significance tests
- [ ] Analyze learning curves
- [ ] Create evaluation dashboard

---

 Day 5: Pipeline Construction & Model Selection
Lecture: [04_pipeline_construction.md](./lecture_notes/04_pipeline_construction.md)

Topics:
- Scikit-learn Pipelines with preprocessing
- ColumnTransformer for mixed data
- Hyperparameter tuning in pipelines
- Model serialization (joblib)
- Production deployment preparation

Hands-On:
```python
from lecture_notes import ComprehensiveMLPipeline

 Build production pipeline
pipeline_builder = ComprehensiveMLPipeline(
    model_type='random_forest',
    problem_type='classification'
)

pipeline = pipeline_builder.build_pipeline(
    numerical_features=num_cols,
    categorical_features=cat_cols,
    imputation_strategy='median',
    feature_selection='k_best'
)

pipeline_builder.fit(X_train, y_train)
predictions = pipeline_builder.predict(X_test)
```

Practice:
- [ ] Build end-to-end pipeline
- [ ] Tune hyperparameters (Grid/Random search)
- [ ] Serialize and load model
- [ ] Create FastAPI deployment endpoint
- [ ] Validate pipeline on test data

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_regression_models.md](./lecture_notes/01_regression_models.md) - Comprehensive regression with diagnostics
   - [02_classification_algorithms.md](./lecture_notes/02_classification_algorithms.md) - Classification with imbalanced data handling
   - [03_model_evaluation.md](./lecture_notes/03_model_evaluation.md) - Statistical evaluation and testing
   - [04_pipeline_construction.md](./lecture_notes/04_pipeline_construction.md) - Production pipelines

2. External Resources:
   - "Hands-On Machine Learning" (Aurélien Géron) - Chapters 4-6
   - "Introduction to Statistical Learning" (ISLR) - Chapters 3-8
   - Scikit-learn Documentation - Model Selection and Evaluation
   - XGBoost & LightGBM Documentation

 Key Libraries
```python
 Core ML
from sklearn.linear_model import (
    LinearRegression, LogisticRegression,
    Ridge, Lasso, ElasticNet
)
from sklearn.tree import DecisionTreeClassifier, DecisionTreeRegressor
from sklearn.ensemble import (
    RandomForestClassifier, GradientBoostingClassifier,
    AdaBoostClassifier, VotingClassifier, StackingClassifier
)
from sklearn.svm import SVC, SVR
from sklearn.neighbors import KNeighborsClassifier
from sklearn.naive_bayes import GaussianNB

 Advanced Boosting
import xgboost as xgb
import lightgbm as lgb

 Imbalanced Data
from imblearn.over_sampling import SMOTE, ADASYN
from imblearn.under_sampling import RandomUnderSampler

 Evaluation
from sklearn.model_selection import (
    cross_val_score, StratifiedKFold,
    GridSearchCV, RandomizedSearchCV,
    learning_curve
)
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    roc_auc_score, confusion_matrix, classification_report,
    mean_squared_error, r2_score
)
from sklearn.calibration import CalibratedClassifierCV

 Pipelines
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Regression Analysis (20%)
   - 3+ regression models (Linear, Polynomial, Regularized)
   - Residual diagnostics and assumption checks
   - Model comparison with metrics (RMSE, MAE, R²)
   - Feature importance analysis

2. Classification Models (25%)
   - 6+ classification algorithms
   - Imbalanced data handling documentation
   - Probability calibration analysis
   - Decision boundary visualizations

3. Comprehensive Evaluation (25%)
   - Cross-validation results (5-fold stratified)
   - Statistical significance testing (t-test, McNemar)
   - ROC/PR curves for all models
   - Learning curve analysis

4. Production Pipeline (20%)
   - End-to-end sklearn Pipeline
   - Hyperparameter tuning results
   - Model serialization (joblib)
   - Performance benchmarking

5. Final Comparison Report (10%)
   - Model recommendation with justification
   - Feature importance summary
   - Business impact analysis

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [� Lecture Notes](./lecture_notes/) | Algorithm implementations with code |
| [🎯 Exercises](./exercises/) | Hands-on model building |
| [📋 Assignments](./assignments/) | Graded deliverables |
| [📄 Cheatsheets](./cheatsheets/) | Algorithm parameters & tuning |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Review lecture notes and math concepts |
| 45 min | Implement algorithms hands-on |
| 30 min | Hyperparameter tuning experiments |
| 15 min | Document results and insights |

Total Daily Time: ~2 hours

---

 🎓 Key Algorithms to Master

| Algorithm | Type | Best For | Key Hyperparameters |
|-----------|------|----------|---------------------|
| Linear Regression | Regression | Baseline, interpretation | regularization strength |
| Logistic Regression | Classification | Probabilities, speed | C, penalty |
| Decision Tree | Both | Interpretation, speed | max_depth, min_samples |
| Random Forest | Both | General purpose | n_estimators, max_depth |
| SVM | Both | Complex boundaries | C, kernel, gamma |
| XGBoost | Both | Competitions, accuracy | max_depth, learning_rate, n_estimators |
| KNN | Classification | Simple, lazy learning | n_neighbors, metric |
| Naive Bayes | Classification | Text, speed | priors |

---

Previous: [Week 1: Data Preparation ←](../Week_1/README.md) | Next: [Week 3: Advanced ML →](../Week_3/README.md)
