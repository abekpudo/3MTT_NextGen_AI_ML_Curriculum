 Week 3 Cheatsheet: Optimization & Advanced Techniques

 🚀 Quick Reference Guide

---

 🎛️ Hyperparameter Tuning

 Grid Search
```python
from sklearn.model_selection import GridSearchCV

 Define parameter grid
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [5, 10, 15, None],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 4]
}

 Grid Search with Cross-Validation
grid_search = GridSearchCV(
    estimator=RandomForestClassifier(random_state=42),
    param_grid=param_grid,
    cv=5,                       5-fold cross-validation
    scoring='f1_macro',        Evaluation metric
    n_jobs=-1,                 Use all CPU cores
    verbose=2,
    return_train_score=True
)

 Fit and get results
grid_search.fit(X_train, y_train)

 Best parameters and score
print(f"Best params: {grid_search.best_params_}")
print(f"Best score: {grid_search.best_score_:.4f}")

 Best estimator
best_model = grid_search.best_estimator_

 All results
results_df = pd.DataFrame(grid_search.cv_results_)
```

 Random Search
```python
from sklearn.model_selection import RandomizedSearchCV
from scipy.stats import randint, uniform, loguniform

 Define parameter distributions
param_distributions = {
    'n_estimators': randint(50, 500),
    'max_depth': [3, 5, 10, 15, 20, None],
    'min_samples_split': randint(2, 20),
    'min_samples_leaf': randint(1, 10),
    'max_features': ['sqrt', 'log2', None]
}

 Random Search
random_search = RandomizedSearchCV(
    estimator=RandomForestClassifier(random_state=42),
    param_distributions=param_distributions,
    n_iter=100,                Number of parameter settings sampled
    cv=5,
    scoring='f1_macro',
    n_jobs=-1,
    random_state=42,
    verbose=2
)

random_search.fit(X_train, y_train)
best_model = random_search.best_estimator_
```

 Bayesian Optimization with Optuna
```python
import optuna
from optuna.samplers import TPESampler
from optuna.pruners import MedianPruner

 Define objective function
def objective(trial):
     Suggest hyperparameters
    n_estimators = trial.suggest_int('n_estimators', 50, 500)
    max_depth = trial.suggest_int('max_depth', 3, 20)
    min_samples_split = trial.suggest_int('min_samples_split', 2, 20)
    
     Create model
    model = RandomForestClassifier(
        n_estimators=n_estimators,
        max_depth=max_depth,
        min_samples_split=min_samples_split,
        random_state=42
    )
    
     Cross-validation score
    score = cross_val_score(model, X_train, y_train, cv=5, scoring='f1_macro').mean()
    
    return score

 Create study
study = optuna.create_study(
    direction='maximize',
    sampler=TPESampler(),
    pruner=MedianPruner()
)

 Optimize
study.optimize(objective, n_trials=100, timeout=3600)

 Best trial
print(f"Best value: {study.best_value:.4f}")
print(f"Best params: {study.best_params}")

 Visualization
optuna.visualization.plot_optimization_history(study)
optuna.visualization.plot_param_importances(study)
optuna.visualization.plot_slice(study)
```

 Optuna with XGBoost (Advanced)
```python
def xgboost_objective(trial):
    params = {
        'n_estimators': trial.suggest_int('n_estimators', 100, 1000),
        'max_depth': trial.suggest_int('max_depth', 3, 12),
        'learning_rate': trial.suggest_float('learning_rate', 0.01, 0.3, log=True),
        'subsample': trial.suggest_float('subsample', 0.5, 1.0),
        'colsample_bytree': trial.suggest_float('colsample_bytree', 0.5, 1.0),
        'min_child_weight': trial.suggest_int('min_child_weight', 1, 10),
        'gamma': trial.suggest_float('gamma', 1e-8, 1.0, log=True),
        'reg_alpha': trial.suggest_float('reg_alpha', 1e-8, 1.0, log=True),
        'reg_lambda': trial.suggest_float('reg_lambda', 1e-8, 1.0, log=True),
    }
    
    model = xgb.XGBClassifier(params, random_state=42, use_label_encoder=False, eval_metric='logloss')
    
    score = cross_val_score(model, X_train, y_train, cv=5, scoring='f1_macro').mean()
    return score

study = optuna.create_study(direction='maximize')
study.optimize(xgboost_objective, n_trials=50)
```

---

 ⚖️ Regularization Techniques

 L1/L2 Regularization
```python
from sklearn.linear_model import Ridge, Lasso, ElasticNet
from sklearn.preprocessing import StandardScaler

 Standardize features first!
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

 Ridge (L2) - Penalizes large coefficients
ridge = Ridge(alpha=1.0)
ridge.fit(X_train_scaled, y_train)

 Lasso (L1) - Can zero out coefficients (feature selection)
lasso = Lasso(alpha=0.1)
lasso.fit(X_train_scaled, y_train)
selected_features = np.sum(lasso.coef_ != 0)
print(f"Selected {selected_features} features out of {len(lasso.coef_)}")

 Elastic Net - Combines L1 and L2
elastic = ElasticNet(alpha=0.1, l1_ratio=0.5)
elastic.fit(X_train_scaled, y_train)
```

 Finding Optimal Regularization Strength
```python
from sklearn.linear_model import RidgeCV, LassoCV

 Ridge with built-in cross-validation
alphas = np.logspace(-4, 4, 100)
ridge_cv = RidgeCV(alphas=alphas, cv=5, scoring='neg_mean_squared_error')
ridge_cv.fit(X_train_scaled, y_train)

print(f"Optimal alpha: {ridge_cv.alpha_}")

 Lasso with cross-validation
lasso_cv = LassoCV(alphas=alphas, cv=5, random_state=42)
lasso_cv.fit(X_train_scaled, y_train)

print(f"Optimal alpha: {lasso_cv.alpha_}")
```

 Regularization Paths
```python
from sklearn.linear_model import lasso_path, ridge_path

 Lasso Path
alphas_lasso, coefs_lasso, _ = lasso_path(X_train_scaled, y_train, alphas=alphas)

 Plot path
plt.figure(figsize=(10, 6))
for i in range(coefs_lasso.shape[0]):
    plt.plot(alphas_lasso, coefs_lasso[i], label=f'Feature {i}')
plt.xscale('log')
plt.xlabel('Alpha')
plt.ylabel('Coefficient')
plt.title('Lasso Path')
plt.legend()
plt.show()
```

 Early Stopping
```python
from sklearn.ensemble import GradientBoostingClassifier

 With early stopping
model = GradientBoostingClassifier(
    n_estimators=1000,
    learning_rate=0.01,
    max_depth=5,
    validation_fraction=0.1,
    n_iter_no_change=10,      Stop if no improvement for 10 rounds
    tol=1e-4,
    random_state=42
)
model.fit(X_train, y_train)
print(f"Actual iterations: {model.n_estimators_}")
```

---

 🎯 Ensemble Methods

 Bagging (Bootstrap Aggregating)
```python
from sklearn.ensemble import BaggingClassifier, RandomForestClassifier

 Bagging with base estimator
bagging = BaggingClassifier(
    base_estimator=DecisionTreeClassifier(),
    n_estimators=100,
    max_samples=0.8,           80% of training data per estimator
    max_features=0.8,          80% of features per estimator
    bootstrap=True,
    bootstrap_features=False,
    n_jobs=-1,
    random_state=42
)
bagging.fit(X_train, y_train)

 Random Forest (optimized bagging)
rf = RandomForestClassifier(
    n_estimators=200,
    max_depth=15,
    min_samples_split=5,
    max_features='sqrt',
    bootstrap=True,
    oob_score=True,            Out-of-bag score
    n_jobs=-1,
    random_state=42
)
rf.fit(X_train, y_train)
print(f"OOB Score: {rf.oob_score_:.4f}")
```

 Boosting
```python
from sklearn.ensemble import AdaBoostClassifier, GradientBoostingClassifier

 AdaBoost (Adaptive Boosting)
adaboost = AdaBoostClassifier(
    base_estimator=DecisionTreeClassifier(max_depth=3),
    n_estimators=100,
    learning_rate=0.1,
    random_state=42
)
adaboost.fit(X_train, y_train)

 Gradient Boosting
gb = GradientBoostingClassifier(
    n_estimators=100,
    learning_rate=0.1,
    max_depth=3,
    subsample=0.8,
    random_state=42
)
gb.fit(X_train, y_train)

 XGBoost (install: pip install xgboost)
import xgboost as xgb

xgb_model = xgb.XGBClassifier(
    n_estimators=100,
    max_depth=6,
    learning_rate=0.1,
    subsample=0.8,
    colsample_bytree=0.8,
    reg_alpha=0.1,
    reg_lambda=1.0,
    random_state=42,
    use_label_encoder=False,
    eval_metric='logloss'
)
xgb_model.fit(X_train, y_train)

 LightGBM (install: pip install lightgbm)
import lightgbm as lgb

lgb_model = lgb.LGBMClassifier(
    n_estimators=100,
    max_depth=6,
    learning_rate=0.1,
    subsample=0.8,
    colsample_bytree=0.8,
    reg_alpha=0.1,
    reg_lambda=1.0,
    random_state=42
)
lgb_model.fit(X_train, y_train)
```

 Voting Classifier
```python
from sklearn.ensemble import VotingClassifier

 Create individual models
clf1 = LogisticRegression(random_state=42)
clf2 = RandomForestClassifier(n_estimators=100, random_state=42)
clf3 = xgb.XGBClassifier(random_state=42, use_label_encoder=False, eval_metric='logloss')

 Hard voting (majority class)
voting_hard = VotingClassifier(
    estimators=[('lr', clf1), ('rf', clf2), ('xgb', clf3)],
    voting='hard'
)
voting_hard.fit(X_train, y_train)

 Soft voting (weighted probabilities)
voting_soft = VotingClassifier(
    estimators=[('lr', clf1), ('rf', clf2), ('xgb', clf3)],
    voting='soft',
    weights=[1, 2, 2]   Give more weight to RF and XGB
)
voting_soft.fit(X_train, y_train)
```

 Stacking (Stacked Generalization)
```python
from sklearn.ensemble import StackingClassifier
from sklearn.linear_model import LogisticRegression

 Base estimators
estimators = [
    ('rf', RandomForestClassifier(n_estimators=100, random_state=42)),
    ('xgb', xgb.XGBClassifier(random_state=42, use_label_encoder=False, eval_metric='logloss')),
    ('svm', SVC(probability=True, random_state=42))
]

 Stacking with meta-learner
stacking = StackingClassifier(
    estimators=estimators,
    final_estimator=LogisticRegression(),
    cv=5,
    stack_method='predict_proba',   Use probabilities
    n_jobs=-1,
    passthrough=False   Don't pass original features to meta-learner
)
stacking.fit(X_train, y_train)

 Predictions
y_pred = stacking.predict(X_test)
```

---

 📊 MLflow Experiment Tracking

 Basic Tracking
```python
import mlflow
import mlflow.sklearn

 Start experiment
mlflow.set_experiment("hyperparameter_tuning")

with mlflow.start_run(run_name="random_forest_grid"):
     Log parameters
    mlflow.log_param("n_estimators", 100)
    mlflow.log_param("max_depth", 10)
    
     Train model
    model = RandomForestClassifier(n_estimators=100, max_depth=10, random_state=42)
    model.fit(X_train, y_train)
    
     Evaluate
    y_pred = model.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)
    
     Log metrics
    mlflow.log_metric("accuracy", accuracy)
    mlflow.log_metric("f1_score", f1_score(y_test, y_pred, average='macro'))
    
     Log model
    mlflow.sklearn.log_model(model, "model")
```

 Advanced Tracking with Optuna
```python
def objective_with_mlflow(trial):
    with mlflow.start_run(nested=True):
         Log trial number
        mlflow.log_param("trial_number", trial.number)
        
         Suggest hyperparameters
        params = {
            'n_estimators': trial.suggest_int('n_estimators', 50, 500),
            'max_depth': trial.suggest_int('max_depth', 3, 20),
            'min_samples_split': trial.suggest_int('min_samples_split', 2, 20)
        }
        
         Log all parameters
        for key, value in params.items():
            mlflow.log_param(key, value)
        
         Train and evaluate
        model = RandomForestClassifier(params, random_state=42)
        score = cross_val_score(model, X_train, y_train, cv=5, scoring='f1_macro').mean()
        
         Log score
        mlflow.log_metric("f1_macro", score)
        
        return score

 Run optimization with parent run
with mlflow.start_run(run_name="optuna_optimization"):
    study = optuna.create_study(direction='maximize')
    study.optimize(objective_with_mlflow, n_trials=50)
    
     Log best result
    mlflow.log_params(study.best_params)
    mlflow.log_metric("best_score", study.best_value)
```

---

 📈 Model Comparison

 Comprehensive Comparison
```python
def compare_all_models(models_dict, X_train, X_test, y_train, y_test):
    """Compare multiple models across all metrics"""
    results = []
    
    for name, model in models_dict.items():
         Train
        start_time = time.time()
        model.fit(X_train, y_train)
        train_time = time.time() - start_time
        
         Predict
        y_pred = model.predict(X_test)
        y_prob = model.predict_proba(X_test)[:, 1] if hasattr(model, 'predict_proba') else None
        
         Metrics
        result = {
            'Model': name,
            'Accuracy': accuracy_score(y_test, y_pred),
            'Precision': precision_score(y_test, y_pred, average='weighted'),
            'Recall': recall_score(y_test, y_pred, average='weighted'),
            'F1': f1_score(y_test, y_pred, average='weighted'),
            'ROC AUC': roc_auc_score(y_test, y_prob) if y_prob is not None else None,
            'Train Time (s)': train_time
        }
        results.append(result)
    
    return pd.DataFrame(results)

 Usage
models = {
    'Random Forest': RandomForestClassifier(random_state=42),
    'XGBoost': xgb.XGBClassifier(random_state=42, use_label_encoder=False, eval_metric='logloss'),
    'LightGBM': lgb.LGBMClassifier(random_state=42),
    'Voting': voting_soft,
    'Stacking': stacking
}

comparison = compare_all_models(models, X_train, X_test, y_train, y_test)
print(comparison.sort_values('F1', ascending=False))
```

---

 🎯 Hyperparameter Reference Tables

 Decision Tree
```
Parameter         | Typical Range    | Effect
------------------|------------------|-----------------------------------------
max_depth         | 3-50 or None     | Tree depth, None = unlimited
min_samples_split | 2-20             | Min samples to split node
min_samples_leaf  | 1-20             | Min samples in leaf node
max_features      | sqrt/log2/None   | Features considered at each split
criterion         | gini/entropy     | Split quality measure
```

 Random Forest
```
Parameter         | Typical Range    | Effect
------------------|------------------|-----------------------------------------
n_estimators      | 50-500           | Number of trees
max_depth         | 3-50 or None     | Tree depth
min_samples_split | 2-20             | Min samples to split
min_samples_leaf  | 1-20             | Min samples in leaf
max_features      | sqrt/log2/None   | Features at each split
bootstrap         | True/False       | Bootstrap sampling
class_weight      | balanced/None    | Handle imbalance
```

 XGBoost/LightGBM
```
Parameter         | Typical Range    | Effect
------------------|------------------|-----------------------------------------
n_estimators      | 100-1000         | Number of trees (boosting rounds)
max_depth         | 3-12             | Tree depth (prevents overfitting)
learning_rate     | 0.01-0.3         | Shrinkage (slower = better generalization)
subsample         | 0.5-1.0          | Row sampling ratio
colsample_bytree  | 0.5-1.0          | Column sampling ratio
reg_alpha         | 0-1              | L1 regularization
reg_lambda        | 0-1              | L2 regularization
min_child_weight  | 1-10             | Min sum of instance weight
```

 SVM
```
Parameter         | Typical Range    | Effect
------------------|------------------|-----------------------------------------
C                 | 0.1-1000         | Regularization (small = more regularization)
kernel            | rbf/linear/poly  | Kernel type
gamma             | 0.001-1.0        | Kernel coefficient (scale/auto)
degree            | 2-5              | Polynomial degree (for poly kernel)
class_weight      | balanced/None    | Handle imbalance
```

---

 ⚠️ Common Tuning Pitfalls

| Pitfall | Problem | Solution |
|---------|---------|----------|
| Too many parameters | Exponential search space | Use Random or Bayesian search |
| Wide ranges | Inefficient sampling | Use log scale for learning rates |
| Ignoring dependencies | Suboptimal combinations | Use conditional parameters (Optuna) |
| No early stopping | Wasted computation | Use pruning in Optuna |
| Tuning on test set | Overfitting to test data | Use validation set or CV |
| Fixed random seed | Biased results | Average over multiple seeds |
| Ignoring training time | Slow deployment | Consider inference speed |

---

 🔧 Quick Commands

| Task | Command |
|------|---------|
| Grid Search | `GridSearchCV(estimator, param_grid, cv=5)` |
| Random Search | `RandomizedSearchCV(estimator, param_dist, n_iter=100)` |
| Optuna Study | `optuna.create_study(direction='maximize')` |
| Ridge | `Ridge(alpha=1.0)` |
| Lasso | `Lasso(alpha=0.1)` |
| Random Forest | `RandomForestClassifier(n_estimators=100)` |
| Voting | `VotingClassifier(estimators, voting='soft')` |
| Stacking | `StackingClassifier(estimators, final_estimator)` |
| MLflow Log | `mlflow.log_param('lr', 0.01)` |

---

Previous: [Week 2 Cheatsheet ←](../Week_2/cheatsheets/01_cheatsheet.md) | Next: [Week 4 Cheatsheet →](../Week_4/cheatsheets/01_cheatsheet.md)
