 Week 2 Cheatsheet: ML Algorithms & Model Evaluation

 🚀 Quick Reference Guide

---

 🔄 Regression Algorithms

 Linear Models
```python
from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet

 Basic Linear Regression
lr = LinearRegression()
lr.fit(X_train, y_train)
predictions = lr.predict(X_test)
coefficients = lr.coef_
intercept = lr.intercept_

 Regularized Regression
 Ridge (L2) - Handles multicollinearity
ridge = Ridge(alpha=1.0)
ridge.fit(X_train, y_train)

 Lasso (L1) - Feature selection
lasso = Lasso(alpha=0.1)
lasso.fit(X_train, y_train)
nonzero_coefs = np.sum(lasso.coef_ != 0)

 Elastic Net (L1 + L2)
enet = ElasticNet(alpha=0.1, l1_ratio=0.5)
enet.fit(X_train, y_train)
```

 Polynomial & Advanced Regression
```python
from sklearn.preprocessing import PolynomialFeatures
from sklearn.pipeline import Pipeline

 Polynomial Regression
poly_reg = Pipeline([
    ('poly', PolynomialFeatures(degree=3)),
    ('linear', LinearRegression())
])
poly_reg.fit(X_train, y_train)

 Regression Metrics
from sklearn.metrics import mean_squared_error, r2_score, mean_absolute_error

mse = mean_squared_error(y_true, y_pred)
rmse = np.sqrt(mse)
mae = mean_absolute_error(y_true, y_pred)
r2 = r2_score(y_true, y_pred)
adjusted_r2 = 1 - (1-r2)(n-1)/(n-p-1)   n=samples, p=features
```

 Residual Diagnostics
```python
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

 Calculate residuals
residuals = y_true - y_pred

 Diagnostic plots
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

 1. Residuals vs Fitted
axes[0, 0].scatter(y_pred, residuals, alpha=0.6)
axes[0, 0].axhline(y=0, color='r', linestyle='--')
axes[0, 0].set_xlabel('Fitted values')
axes[0, 0].set_ylabel('Residuals')

 2. Q-Q Plot
stats.probplot(residuals, dist="norm", plot=axes[0, 1])

 3. Scale-Location (Sqrt of standardized residuals)
std_residuals = residuals / np.std(residuals)
axes[1, 0].scatter(y_pred, np.sqrt(np.abs(std_residuals)), alpha=0.6)

 4. Residual Distribution
sns.histplot(residuals, kde=True, ax=axes[1, 1])
axes[1, 1].set_xlabel('Residuals')

plt.tight_layout()
```

---

 🎯 Classification Algorithms

 Logistic Regression
```python
from sklearn.linear_model import LogisticRegression
from sklearn.calibration import CalibratedClassifierCV

 Basic Logistic Regression
log_reg = LogisticRegression(
    C=1.0,                     Inverse regularization strength
    penalty='l2',              Regularization type
    solver='lbfgs',            Optimization algorithm
    max_iter=1000,
    random_state=42
)
log_reg.fit(X_train, y_train)

 Probability calibration
log_reg_calibrated = CalibratedClassifierCV(log_reg, method='sigmoid', cv=5)
log_reg_calibrated.fit(X_train, y_train)

 Predictions
y_pred = log_reg.predict(X_test)
y_prob = log_reg.predict_proba(X_test)[:, 1]   Probability of positive class
```

 Tree-Based Models
```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier

 Decision Tree
dt = DecisionTreeClassifier(
    max_depth=10,              Prevent overfitting
    min_samples_split=5,     Min samples to split
    min_samples_leaf=2,      Min samples in leaf
    criterion='gini',          'gini' or 'entropy'
    random_state=42
)
dt.fit(X_train, y_train)

 Feature importance
tree_importance = pd.DataFrame({
    'feature': feature_names,
    'importance': dt.feature_importances_
}).sort_values('importance', ascending=False)

 Random Forest
rf = RandomForestClassifier(
    n_estimators=100,          Number of trees
    max_depth=None,
    min_samples_split=2,
    min_samples_leaf=1,
    max_features='sqrt',       Features considered at each split
    bootstrap=True,            Use bootstrap samples
    oob_score=True,           Out-of-bag score
    n_jobs=-1,                Use all CPU cores
    random_state=42
)
rf.fit(X_train, y_train)

 OOB Score (built-in validation)
print(f"OOB Score: {rf.oob_score_:.4f}")

 Gradient Boosting
gb = GradientBoostingClassifier(
    n_estimators=100,
    learning_rate=0.1,         Shrinkage
    max_depth=3,              Tree depth
    subsample=0.8,            Stochastic gradient boosting
    random_state=42
)
gb.fit(X_train, y_train)
```

 Support Vector Machines
```python
from sklearn.svm import SVC

 Linear SVM (fast, good for high-dimensional data)
svm_linear = SVC(
    kernel='linear',
    C=1.0,                    Regularization
    probability=True,        Enable probability estimates
    random_state=42
)

 RBF Kernel (non-linear boundaries)
svm_rbf = SVC(
    kernel='rbf',
    C=1.0,
    gamma='scale',            Kernel coefficient
    probability=True,
    random_state=42
)

 Polynomial Kernel
svm_poly = SVC(
    kernel='poly',
    degree=3,                Polynomial degree
    C=1.0,
    probability=True,
    random_state=42
)

svm_rbf.fit(X_train, y_train)
y_pred = svm_rbf.predict(X_test)
y_prob = svm_rbf.predict_proba(X_test)
```

 Other Classifiers
```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.naive_bayes import GaussianNB

 K-Nearest Neighbors
knn = KNeighborsClassifier(
    n_neighbors=5,
    weights='uniform',        'uniform' or 'distance'
    metric='minkowski',       Distance metric
    p=2                      2 = Euclidean, 1 = Manhattan
)
knn.fit(X_train, y_train)

 Naive Bayes
nb = GaussianNB()
nb.fit(X_train, y_train)

 XGBoost (if installed)
import xgboost as xgb

xgb_clf = xgb.XGBClassifier(
    n_estimators=100,
    max_depth=6,
    learning_rate=0.1,
    subsample=0.8,
    colsample_bytree=0.8,
    random_state=42,
    use_label_encoder=False,
    eval_metric='logloss'
)
xgb_clf.fit(X_train, y_train)
```

---

 ⚖️ Handling Imbalanced Data

 Sampling Techniques
```python
from imblearn.over_sampling import SMOTE, ADASYN, RandomOverSampler
from imblearn.under_sampling import RandomUnderSampler
from imblearn.combine import SMOTETomek

 SMOTE (Synthetic Minority Over-sampling)
smote = SMOTE(random_state=42, k_neighbors=5)
X_resampled, y_resampled = smote.fit_resample(X_train, y_train)

 ADASYN (Adaptive Synthetic Sampling)
adasyn = ADASYN(random_state=42, n_neighbors=5)
X_resampled, y_resampled = adasyn.fit_resample(X_train, y_train)

 Random Undersampling
undersampler = RandomUnderSampler(random_state=42)
X_resampled, y_resampled = undersampler.fit_resample(X_train, y_train)

 Combined: SMOTE + Tomek Links
smote_tomek = SMOTETomek(random_state=42)
X_resampled, y_resampled = smote_tomek.fit_resample(X_train, y_train)
```

 Cost-Sensitive Learning
```python
from sklearn.utils.class_weight import compute_class_weight

 Compute class weights
class_weights = compute_class_weight(
    'balanced',
    classes=np.unique(y_train),
    y=y_train
)
class_weight_dict = dict(enumerate(class_weights))

 Use in algorithms
rf_weighted = RandomForestClassifier(
    class_weight='balanced',   or class_weight_dict
    random_state=42
)

 Manual class weights
svm_weighted = SVC(
    class_weight={0: 1, 1: 5},   Penalize class 1 errors 5x more
    random_state=42
)
```

---

 📊 Evaluation Metrics

 Classification Metrics
```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    roc_auc_score, average_precision_score, 
    confusion_matrix, classification_report,
    precision_recall_curve, roc_curve,
    log_loss
)

 Basic Metrics
accuracy = accuracy_score(y_true, y_pred)
precision = precision_score(y_true, y_pred, average='weighted')
recall = recall_score(y_true, y_pred, average='weighted')
f1 = f1_score(y_true, y_pred, average='weighted')

 For imbalanced data
f1_macro = f1_score(y_true, y_pred, average='macro')
f1_weighted = f1_score(y_true, y_pred, average='weighted')

 Probability-based metrics
roc_auc = roc_auc_score(y_true, y_prob)
avg_precision = average_precision_score(y_true, y_prob)
logloss = log_loss(y_true, y_prob)

 Confusion Matrix
cm = confusion_matrix(y_true, y_pred)
tn, fp, fn, tp = cm.ravel()

 Classification Report (all metrics)
print(classification_report(y_true, y_pred, target_names=['Class 0', 'Class 1']))
```

 ROC and Precision-Recall Curves
```python
from sklearn.metrics import roc_curve, precision_recall_curve, auc

 ROC Curve
fpr, tpr, thresholds = roc_curve(y_true, y_prob)
roc_auc = auc(fpr, tpr)

plt.figure(figsize=(8, 6))
plt.plot(fpr, tpr, color='darkorange', lw=2, label=f'ROC curve (AUC = {roc_auc:.2f})')
plt.plot([0, 1], [0, 1], color='navy', lw=2, linestyle='--', label='Random')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc='lower right')

 Precision-Recall Curve (better for imbalanced data)
precision, recall, thresholds = precision_recall_curve(y_true, y_prob)
pr_auc = auc(recall, precision)

plt.figure(figsize=(8, 6))
plt.plot(recall, precision, color='blue', lw=2, label=f'PR curve (AUC = {pr_auc:.2f})')
baseline = np.sum(y_true) / len(y_true)
plt.axhline(y=baseline, color='red', linestyle='--', label=f'Baseline ({baseline:.2f})')
plt.xlabel('Recall')
plt.ylabel('Precision')
plt.title('Precision-Recall Curve')
plt.legend()
```

 Regression Metrics Summary
```python
 All regression metrics in one place
from sklearn.metrics import (
    mean_squared_error, mean_absolute_error, 
    r2_score, explained_variance_score,
    mean_squared_log_error
)

def regression_metrics(y_true, y_pred):
    """Calculate all regression metrics"""
    mse = mean_squared_error(y_true, y_pred)
    metrics = {
        'MSE': mse,
        'RMSE': np.sqrt(mse),
        'MAE': mean_absolute_error(y_true, y_pred),
        'R²': r2_score(y_true, y_pred),
        'Explained Variance': explained_variance_score(y_true, y_pred)
    }
    return metrics
```

---

 🔍 Cross-Validation

 Basic CV
```python
from sklearn.model_selection import (
    cross_val_score, StratifiedKFold, 
    KFold, cross_validate
)

 Simple cross-validation
cv_scores = cross_val_score(model, X, y, cv=5, scoring='accuracy')
print(f"CV Accuracy: {cv_scores.mean():.4f} (+/- {cv_scores.std()  2:.4f})")

 Stratified K-Fold (for classification)
skfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
cv_scores = cross_val_score(model, X, y, cv=skfold, scoring='f1_macro')

 Multiple metrics with cross_validate
scoring = ['accuracy', 'precision_macro', 'recall_macro', 'f1_macro']
cv_results = cross_validate(model, X, y, cv=5, scoring=scoring)
```

 Nested Cross-Validation
```python
from sklearn.model_selection import GridSearchCV, cross_val_score

 Outer loop: Model evaluation
outer_cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

 Inner loop: Hyperparameter tuning
inner_cv = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)

param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [3, 5, 10, None]
}

nested_scores = []
for train_idx, test_idx in outer_cv.split(X, y):
    X_train, X_test = X[train_idx], X[test_idx]
    y_train, y_test = y[train_idx], y[test_idx]
    
     Inner CV for hyperparameter tuning
    clf = GridSearchCV(
        RandomForestClassifier(random_state=42),
        param_grid,
        cv=inner_cv,
        scoring='f1_macro'
    )
    clf.fit(X_train, y_train)
    
     Evaluate on outer test set
    score = clf.score(X_test, y_test)
    nested_scores.append(score)

print(f"Nested CV Score: {np.mean(nested_scores):.4f}")
```

---

 📈 Learning Curves

 Plot Learning Curves
```python
from sklearn.model_selection import learning_curve

def plot_learning_curve(estimator, X, y, cv=5):
    """Plot learning curves to diagnose bias/variance"""
    
    train_sizes, train_scores, val_scores = learning_curve(
        estimator, X, y, cv=cv, n_jobs=-1,
        train_sizes=np.linspace(0.1, 1.0, 10),
        scoring='accuracy'
    )
    
    train_mean = np.mean(train_scores, axis=1)
    train_std = np.std(train_scores, axis=1)
    val_mean = np.mean(val_scores, axis=1)
    val_std = np.std(val_scores, axis=1)
    
    plt.figure(figsize=(10, 6))
    plt.plot(train_sizes, train_mean, 'o-', color='blue', label='Training Score')
    plt.fill_between(train_sizes, train_mean - train_std, train_mean + train_std, alpha=0.1, color='blue')
    plt.plot(train_sizes, val_mean, 'o-', color='red', label='Validation Score')
    plt.fill_between(train_sizes, val_mean - val_std, val_mean + val_std, alpha=0.1, color='red')
    plt.xlabel('Training Set Size')
    plt.ylabel('Score')
    plt.title('Learning Curves')
    plt.legend(loc='best')
    plt.grid(True)
    
     Interpretation
    if train_mean[-1] < 0.8 and val_mean[-1] < 0.8:
        print("High Bias: Underfitting. Use more complex model or better features.")
    elif train_mean[-1] > 0.9 and val_mean[-1] < 0.8:
        print("High Variance: Overfitting. Use regularization or more data.")
    else:
        print("Good fit!")

plot_learning_curve(RandomForestClassifier(), X, y)
```

---

 ⚖️ Statistical Model Comparison

 Paired t-test
```python
from scipy import stats

 Compare two models using cross-validation scores
model1_scores = cross_val_score(model1, X, y, cv=10, scoring='accuracy')
model2_scores = cross_val_score(model2, X, y, cv=10, scoring='accuracy')

 Paired t-test
t_stat, p_value = stats.ttest_rel(model1_scores, model2_scores)

alpha = 0.05
if p_value < alpha:
    print(f"Significant difference (p={p_value:.4f})")
    if model1_scores.mean() > model2_scores.mean():
        print("Model 1 is significantly better")
    else:
        print("Model 2 is significantly better")
else:
    print(f"No significant difference (p={p_value:.4f})")
```

 McNemar's Test
```python
from statsmodels.stats.contingency_tables import mcnemar

 McNemar's test for comparing two classifiers on same test set
def mcnemar_test(y_true, model1_pred, model2_pred):
    """
    McNemar's test for comparing two models
    """
     Create contingency table
    correct1 = (model1_pred == y_true)
    correct2 = (model2_pred == y_true)
    
     b: model1 correct, model2 wrong
     c: model1 wrong, model2 correct
    b = np.sum(correct1 & ~correct2)
    c = np.sum(~correct1 & correct2)
    
     McNemar test
    table = [[0, b], [c, 0]]
    result = mcnemar(table, exact=True)
    
    return result.pvalue

p_value = mcnemar_test(y_test, model1_pred, model2_pred)
```

---

 🔧 Model Selection Workflow

 Complete Comparison Pipeline
```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from scipy.stats import randint, uniform

def compare_models(models_dict, X_train, X_test, y_train, y_test):
    """Compare multiple models and return results"""
    
    results = []
    
    for name, model in models_dict.items():
         Fit model
        model.fit(X_train, y_train)
        
         Predictions
        y_pred = model.predict(X_test)
        y_prob = model.predict_proba(X_test)[:, 1] if hasattr(model, 'predict_proba') else None
        
         Metrics
        result = {
            'Model': name,
            'Accuracy': accuracy_score(y_test, y_pred),
            'Precision': precision_score(y_test, y_pred, average='weighted'),
            'Recall': recall_score(y_test, y_pred, average='weighted'),
            'F1': f1_score(y_test, y_pred, average='weighted'),
            'ROC AUC': roc_auc_score(y_test, y_prob) if y_prob is not None else None
        }
        results.append(result)
    
    return pd.DataFrame(results)

 Usage
models = {
    'Logistic Regression': LogisticRegression(random_state=42),
    'Random Forest': RandomForestClassifier(random_state=42),
    'SVM': SVC(probability=True, random_state=42),
    'XGBoost': xgb.XGBClassifier(random_state=42)
}

comparison_df = compare_models(models, X_train, X_test, y_train, y_test)
print(comparison_df.sort_values('F1', ascending=False))
```

---

 📚 Essential Command Reference

| Task | Code Pattern |
|------|--------------|
| Train model | `model.fit(X_train, y_train)` |
| Predict | `model.predict(X_test)` |
| Predict probability | `model.predict_proba(X_test)` |
| Cross-validation | `cross_val_score(model, X, y, cv=5)` |
| Grid search | `GridSearchCV(model, param_grid, cv=5)` |
| Classification report | `classification_report(y_true, y_pred)` |
| Confusion matrix | `confusion_matrix(y_true, y_pred)` |
| ROC AUC | `roc_auc_score(y_true, y_prob)` |
| Feature importance | `model.feature_importances_` |

---

 ⚠️ Common Pitfalls & Solutions

| Pitfall | Problem | Solution |
|---------|---------|----------|
| Data Leakage | Using test data during preprocessing | Always split first, fit on train only |
| Imbalanced metrics | Accuracy misleading with imbalance | Use F1, ROC-AUC, or PR-AUC |
| Overfitting | Model memorizes training data | Use regularization, cross-validation, more data |
| Underfitting | Model too simple | Increase complexity, better features |
| Wrong CV | Random split for time series | Use TimeSeriesSplit |
| Tuning on test | Overfitting to test set | Use validation set or nested CV |

---

 🎯 Quick Decision Tree

Classification Problem?
- Small dataset (< 1000): Logistic Regression or Naive Bayes
- Medium dataset (1K - 100K): Random Forest or Gradient Boosting
- Large dataset (> 100K): XGBoost or LightGBM
- Need interpretability: Decision Tree or Logistic Regression
- Complex boundaries: SVM with RBF kernel

Regression Problem?
- Linear relationship: Linear Regression
- Non-linear: Random Forest or Gradient Boosting
- Need uncertainty: Bayesian approaches
- Regularization needed: Ridge or Lasso

Imbalanced Data?
- Try SMOTE + ensemble methods
- Use class weights
- Focus on PR-AUC instead of accuracy
- Consider threshold tuning

---

Previous: [Week 1 Cheatsheet ←](../Week_1/cheatsheets/01_cheatsheet.md) | Next: [Week 3 Cheatsheet →](../Week_3/cheatsheets/01_cheatsheet.md)
