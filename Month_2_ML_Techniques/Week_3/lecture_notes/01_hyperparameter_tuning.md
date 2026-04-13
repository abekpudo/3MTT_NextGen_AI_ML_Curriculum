 Hyperparameter Tuning: Optimizing Model Performance

 1. Introduction
Hyperparameter tuning is the process of finding the optimal configuration for machine learning models that cannot be learned from the data. This lecture covers systematic search strategies including Grid Search, Random Search, Bayesian Optimization, and modern automated tuning approaches with comprehensive implementations.

 2. Why Hyperparameter Tuning Matters
- Performance Optimization: Proper tuning can improve model accuracy by 10-30%
- Overfitting Prevention: Regularization parameters control model complexity
- Computational Efficiency: Find optimal trade-offs between accuracy and training time
- Resource Optimization: Minimize training costs while maximizing performance
- Reproducibility: Documented hyperparameters enable consistent results

 3. Hyperparameter Search Strategies

 3.1. Complete Tuning Framework
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import (
    GridSearchCV, RandomizedSearchCV,
    cross_val_score, StratifiedKFold
)
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.linear_model import LogisticRegression, Ridge, Lasso
from sklearn.svm import SVC
from sklearn.metrics import (
    accuracy_score, f1_score, roc_auc_score,
    make_scorer, classification_report
)
from scipy.stats import randint, uniform, loguniform
import optuna
from optuna.samplers import TPESampler
import warnings
warnings.filterwarnings('ignore')

class ComprehensiveHyperparameterTuner:
    """Complete hyperparameter tuning framework"""
    
    def __init__(self, model, param_distributions, problem_type='classification'):
        self.model = model
        self.param_distributions = param_distributions
        self.problem_type = problem_type
        self.best_params = None
        self.best_score = None
        self.search_results = None
    
    def grid_search(self, X, y, param_grid=None, cv=5, scoring='f1_macro', n_jobs=-1):
        """Exhaustive grid search over specified parameter values"""
        
        if param_grid is None:
            param_grid = self.param_distributions
        
        print("🔍 Running Grid Search...")
        print(f"Total combinations: {np.prod([len(v) for v in param_grid.values()])}")
        
        grid_search = GridSearchCV(
            self.model, param_grid,
            cv=cv, scoring=scoring,
            n_jobs=n_jobs, verbose=1,
            return_train_score=True
        )
        
        grid_search.fit(X, y)
        
        self.best_params = grid_search.best_params_
        self.best_score = grid_search.best_score_
        self.search_results = pd.DataFrame(grid_search.cv_results_)
        
        print(f"\n✅ Best Score: {self.best_score:.4f}")
        print(f"Best Parameters: {self.best_params}")
        
        return grid_search.best_estimator_, self.search_results
    
    def random_search(self, X, y, n_iter=100, cv=5, scoring='f1_macro', 
                     random_state=42, n_jobs=-1):
        """Randomized search on hyperparameters"""
        
        print(f"🔍 Running Random Search (n_iter={n_iter})...")
        
        random_search = RandomizedSearchCV(
            self.model, self.param_distributions,
            n_iter=n_iter, cv=cv, scoring=scoring,
            random_state=random_state, n_jobs=n_jobs,
            verbose=1, return_train_score=True
        )
        
        random_search.fit(X, y)
        
        self.best_params = random_search.best_params_
        self.best_score = random_search.best_score_
        self.search_results = pd.DataFrame(random_search.cv_results_)
        
        print(f"\n✅ Best Score: {self.best_score:.4f}")
        print(f"Best Parameters: {self.best_params}")
        
        return random_search.best_estimator_, self.search_results
    
    def plot_search_results(self, param_name=None):
        """Visualize hyperparameter search results"""
        if self.search_results is None:
            raise ValueError("Run search first")
        
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        
         1. Score distribution
        axes[0, 0].hist(self.search_results['mean_test_score'], bins=20, edgecolor='black')
        axes[0, 0].axvline(self.best_score, color='red', linestyle='--', 
                          label=f'Best: {self.best_score:.4f}')
        axes[0, 0].set_xlabel('Mean Test Score')
        axes[0, 0].set_ylabel('Frequency')
        axes[0, 0].set_title('Score Distribution')
        axes[0, 0].legend()
        axes[0, 0].grid(True, alpha=0.3)
        
         2. Score vs iteration
        axes[0, 1].plot(self.search_results['mean_test_score'], 'o-', alpha=0.6)
        axes[0, 1].set_xlabel('Iteration')
        axes[0, 1].set_ylabel('Mean Test Score')
        axes[0, 1].set_title('Score Progression')
        axes[0, 1].grid(True, alpha=0.3)
        
         3. Train vs Test scores
        axes[1, 0].scatter(self.search_results['mean_train_score'],
                          self.search_results['mean_test_score'],
                          alpha=0.6)
        axes[1, 0].plot([0, 1], [0, 1], 'k--', label='Perfect fit')
        axes[1, 0].set_xlabel('Mean Train Score')
        axes[1, 0].set_ylabel('Mean Test Score')
        axes[1, 0].set_title('Train vs Test Scores')
        axes[1, 0].legend()
        axes[1, 0].grid(True, alpha=0.3)
        
         4. Parameter importance (if specified)
        if param_name and f'param_{param_name}' in self.search_results.columns:
            param_values = self.search_results[f'param_{param_name}']
            if param_values.dtype in ['int64', 'float64']:
                axes[1, 1].scatter(param_values, self.search_results['mean_test_score'])
                axes[1, 1].set_xlabel(param_name)
                axes[1, 1].set_ylabel('Mean Test Score')
                axes[1, 1].set_title(f'Score vs {param_name}')
                axes[1, 1].grid(True, alpha=0.3)
        else:
            axes[1, 1].text(0.5, 0.5, 'Parameter plot\n(not available)',
                          ha='center', va='center', transform=axes[1, 1].transAxes)
        
        plt.tight_layout()
        return fig

 Usage
param_dist_rf = {
    'n_estimators': randint(50, 500),
    'max_depth': [3, 5, 7, 10, 15, 20, None],
    'min_samples_split': randint(2, 20),
    'min_samples_leaf': randint(1, 10),
    'max_features': ['sqrt', 'log2', None]
}

tuner = ComprehensiveHyperparameterTuner(
    RandomForestClassifier(random_state=42),
    param_dist_rf
)
best_model, results = tuner.random_search(X_train, y_train, n_iter=50)
tuner.plot_search_results(param_name='n_estimators')
```

 4. Bayesian Optimization with Optuna

 4.1. Advanced Bayesian Optimization
```python
class OptunaHyperparameterOptimizer:
    """Bayesian optimization using Optuna"""
    
    def __init__(self, model_type='random_forest', n_trials=100, direction='maximize'):
        self.model_type = model_type
        self.n_trials = n_trials
        self.direction = direction
        self.study = None
        self.best_params = None
    
    def create_objective(self, X, y, cv=5):
        """Create Optuna objective function"""
        
        def objective(trial):
             Suggest hyperparameters based on model type
            if self.model_type == 'random_forest':
                params = {
                    'n_estimators': trial.suggest_int('n_estimators', 50, 500),
                    'max_depth': trial.suggest_int('max_depth', 3, 30),
                    'min_samples_split': trial.suggest_int('min_samples_split', 2, 20),
                    'min_samples_leaf': trial.suggest_int('min_samples_leaf', 1, 10),
                    'max_features': trial.suggest_categorical('max_features', ['sqrt', 'log2', None])
                }
                model = RandomForestClassifier(params, random_state=42)
                
            elif self.model_type == 'gradient_boosting':
                params = {
                    'n_estimators': trial.suggest_int('n_estimators', 50, 500),
                    'learning_rate': trial.suggest_float('learning_rate', 0.01, 0.3, log=True),
                    'max_depth': trial.suggest_int('max_depth', 3, 10),
                    'subsample': trial.suggest_float('subsample', 0.6, 1.0),
                    'min_samples_split': trial.suggest_int('min_samples_split', 2, 20)
                }
                model = GradientBoostingClassifier(params, random_state=42)
                
            elif self.model_type == 'svm':
                params = {
                    'C': trial.suggest_float('C', 1e-3, 1e3, log=True),
                    'gamma': trial.suggest_categorical('gamma', ['scale', 'auto']),
                    'kernel': trial.suggest_categorical('kernel', ['rbf', 'poly', 'sigmoid'])
                }
                model = SVC(params, probability=True, random_state=42)
                
            elif self.model_type == 'logistic':
                params = {
                    'C': trial.suggest_float('C', 1e-4, 1e4, log=True),
                    'penalty': trial.suggest_categorical('penalty', ['l1', 'l2', 'elasticnet']),
                    'solver': 'saga'
                }
                model = LogisticRegression(params, max_iter=1000, random_state=42)
            
             Cross-validation
            scores = cross_val_score(model, X, y, cv=cv, scoring='f1_macro')
            
             Report intermediate results for pruning
            trial.report(scores.mean(), step=0)
            if trial.should_prune():
                raise optuna.TrialPruned()
            
            return scores.mean()
        
        return objective
    
    def optimize(self, X, y, cv=5, show_progress=True):
        """Run Bayesian optimization"""
        
        print(f"🔬 Running Bayesian Optimization ({self.model_type})...")
        print(f"Number of trials: {self.n_trials}")
        
         Create study with TPE sampler and pruning
        self.study = optuna.create_study(
            direction=self.direction,
            sampler=TPESampler(seed=42),
            pruner=optuna.pruners.MedianPruner()
        )
        
        objective = self.create_objective(X, y, cv)
        
         Run optimization
        self.study.optimize(
            objective, 
            n_trials=self.n_trials,
            show_progress_bar=show_progress
        )
        
        self.best_params = self.study.best_params
        
        print(f"\n✅ Best Score: {self.study.best_value:.4f}")
        print(f"Best Parameters: {self.best_params}")
        print(f"Completed Trials: {len(self.study.trials)}")
        print(f"Pruned Trials: {len([t for t in self.study.trials if t.state == optuna.trial.TrialState.PRUNED])}")
        
        return self.study.best_params, self.study
    
    def plot_optimization_history(self):
        """Plot optimization history"""
        if self.study is None:
            raise ValueError("Run optimize() first")
        
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        
         1. Optimization history
        optuna.visualization.matplotlib.plot_optimization_history(self.study, ax=axes[0, 0])
        axes[0, 0].set_title('Optimization History')
        
         2. Parameter importances
        try:
            optuna.visualization.matplotlib.plot_param_importances(self.study, ax=axes[0, 1])
            axes[0, 1].set_title('Parameter Importances')
        except:
            axes[0, 1].text(0.5, 0.5, 'Parameter importances\n(not available)',
                          ha='center', va='center', transform=axes[0, 1].transAxes)
        
         3. Slice plot
        try:
            optuna.visualization.matplotlib.plot_slice(self.study, ax=axes[1, 0])
            axes[1, 0].set_title('Parameter Slices')
        except:
            axes[1, 0].text(0.5, 0.5, 'Slice plot\n(not available)',
                          ha='center', va='center', transform=axes[1, 0].transAxes)
        
         4. Contour plot (for first two numeric parameters)
        try:
            numeric_params = [k for k, v in self.best_params.items() 
                            if isinstance(v, (int, float))][:2]
            if len(numeric_params) >= 2:
                optuna.visualization.matplotlib.plot_contour(
                    self.study, params=numeric_params, ax=axes[1, 1]
                )
                axes[1, 1].set_title('Parameter Contours')
            else:
                raise ValueError("Need 2 numeric parameters")
        except:
            axes[1, 1].text(0.5, 0.5, 'Contour plot\n(not available)',
                          ha='center', va='center', transform=axes[1, 1].transAxes)
        
        plt.tight_layout()
        return fig
    
    def get_optimization_report(self):
        """Generate comprehensive optimization report"""
        if self.study is None:
            raise ValueError("Run optimize() first")
        
        report = {
            'best_score': self.study.best_value,
            'best_params': self.study.best_params,
            'n_trials': len(self.study.trials),
            'n_complete': len([t for t in self.study.trials if t.state == optuna.trial.TrialState.COMPLETE]),
            'n_pruned': len([t for t in self.study.trials if t.state == optuna.trial.TrialState.PRUNED]),
            'optimization_time': sum([t.duration.total_seconds() for t in self.study.trials if t.duration]),
            'trial_statistics': {
                'mean_score': np.mean([t.value for t in self.study.trials if t.value is not None]),
                'std_score': np.std([t.value for t in self.study.trials if t.value is not None]),
                'min_score': np.min([t.value for t in self.study.trials if t.value is not None]),
                'max_score': np.max([t.value for t in self.study.trials if t.value is not None])
            }
        }
        
        return report

 Usage
optuna_optimizer = OptunaHyperparameterOptimizer(
    model_type='gradient_boosting',
    n_trials=100
)
best_params, study = optuna_optimizer.optimize(X_train, y_train)
optuna_optimizer.plot_optimization_history()
report = optuna_optimizer.get_optimization_report()
print(report)
```

 5. Model-Specific Hyperparameter Guides

 5.1. Common Model Hyperparameters
```python
class ModelSpecificGuidelines:
    """Hyperparameter guidelines for different model types"""
    
    @staticmethod
    def get_random_forest_params(search_type='random'):
        """Random Forest hyperparameter distributions"""
        if search_type == 'grid':
            return {
                'n_estimators': [50, 100, 200, 300],
                'max_depth': [3, 5, 10, 15, 20, None],
                'min_samples_split': [2, 5, 10],
                'min_samples_leaf': [1, 2, 4],
                'max_features': ['sqrt', 'log2', None],
                'bootstrap': [True, False]
            }
        else:   random/bayesian
            return {
                'n_estimators': randint(50, 500),
                'max_depth': [3, 5, 7, 10, 15, 20, 30, None],
                'min_samples_split': randint(2, 20),
                'min_samples_leaf': randint(1, 10),
                'max_features': ['sqrt', 'log2', None],
                'bootstrap': [True, False]
            }
    
    @staticmethod
    def get_gradient_boosting_params(search_type='random'):
        """Gradient Boosting hyperparameter distributions"""
        if search_type == 'grid':
            return {
                'n_estimators': [50, 100, 200],
                'learning_rate': [0.01, 0.05, 0.1, 0.2],
                'max_depth': [3, 5, 7, 10],
                'subsample': [0.6, 0.8, 1.0],
                'min_samples_split': [2, 5, 10]
            }
        else:
            return {
                'n_estimators': randint(50, 500),
                'learning_rate': loguniform(0.01, 0.3),
                'max_depth': randint(3, 10),
                'subsample': uniform(0.6, 0.4),
                'min_samples_split': randint(2, 20)
            }
    
    @staticmethod
    def get_logistic_regression_params(search_type='random'):
        """Logistic Regression hyperparameter distributions"""
        if search_type == 'grid':
            return {
                'C': [0.001, 0.01, 0.1, 1, 10, 100],
                'penalty': ['l1', 'l2'],
                'solver': ['liblinear', 'saga']
            }
        else:
            return {
                'C': loguniform(1e-4, 1e4),
                'penalty': ['l1', 'l2', 'elasticnet'],
                'l1_ratio': uniform(0, 1)   Only used with elasticnet
            }
    
    @staticmethod
    def get_svm_params(search_type='random'):
        """SVM hyperparameter distributions"""
        if search_type == 'grid':
            return {
                'C': [0.1, 1, 10, 100],
                'gamma': ['scale', 'auto', 0.001, 0.01, 0.1, 1],
                'kernel': ['rbf', 'poly', 'sigmoid']
            }
        else:
            return {
                'C': loguniform(1e-3, 1e3),
                'gamma': loguniform(1e-4, 1e0),
                'kernel': ['rbf', 'poly', 'sigmoid'],
                'degree': randint(2, 6)   For poly kernel
            }

 Usage guidelines
params_rf = ModelSpecificGuidelines.get_random_forest_params('random')
params_gb = ModelSpecificGuidelines.get_gradient_boosting_params('random')
```

 6. Advanced Tuning Strategies

 6.1. Multi-Stage Tuning
```python
class MultiStageTuner:
    """Coarse-to-fine hyperparameter tuning"""
    
    def __init__(self, model, coarse_params, fine_params):
        self.model = model
        self.coarse_params = coarse_params
        self.fine_params = fine_params
    
    def tune(self, X, y, cv=5, scoring='f1_macro'):
        """Two-stage tuning: coarse then fine"""
        
        print("📏 Stage 1: Coarse Search")
        print("="  40)
        
         Coarse search with fewer iterations
        coarse_search = RandomizedSearchCV(
            self.model, self.coarse_params,
            n_iter=20, cv=cv, scoring=scoring,
            random_state=42, n_jobs=-1
        )
        coarse_search.fit(X, y)
        
        print(f"Coarse best: {coarse_search.best_score_:.4f}")
        print(f"Coarse params: {coarse_search.best_params_}")
        
         Fine search around best coarse parameters
        print("\n🔍 Stage 2: Fine Search")
        print("="  40)
        
        fine_param_grid = self._create_fine_grid(coarse_search.best_params_)
        
        fine_search = GridSearchCV(
            coarse_search.best_estimator_,
            fine_param_grid,
            cv=cv, scoring=scoring,
            n_jobs=-1
        )
        fine_search.fit(X, y)
        
        print(f"Fine best: {fine_search.best_score_:.4f}")
        print(f"Fine params: {fine_search.best_params_}")
        
        return fine_search.best_estimator_, {
            'coarse': coarse_search.best_params_,
            'fine': fine_search.best_params_,
            'improvement': fine_search.best_score_ - coarse_search.best_score_
        }
    
    def _create_fine_grid(self, coarse_best):
        """Create fine search grid around coarse best values"""
        fine_grid = {}
        
        for param, value in coarse_best.items():
            if isinstance(value, (int, np.integer)):
                 For integers, search nearby values
                fine_grid[param] = [max(1, value - 2), value - 1, value, value + 1, value + 2]
            elif isinstance(value, (float, np.floating)):
                 For floats, search around in log space
                fine_grid[param] = [value  0.5, value  0.8, value, value  1.2, value  1.5]
            else:
                 For categorical, keep the same
                fine_grid[param] = [value]
        
        return fine_grid

 Usage
multi_stage = MultiStageTuner(
    RandomForestClassifier(random_state=42),
    coarse_params={'n_estimators': randint(50, 300), 'max_depth': [5, 10, 15, None]},
    fine_params=None
)
best_model, tuning_history = multi_stage.tune(X_train, y_train)
```

 7. Best Practices Summary

 7.1. Hyperparameter Tuning Guidelines
```
✅ DO:
- Start with Random Search for large parameter spaces
- Use Bayesian Optimization for expensive models
- Always use cross-validation for reliable estimates
- Consider early stopping/pruning to save time
- Document all search parameters and results
- Validate final model on held-out test set
- Use domain knowledge to constrain search space

❌ DON'T:
- Use test set for hyperparameter selection
- Search too broad a range without justification
- Ignore computational constraints
- Skip nested CV for final performance estimates
- Forget to set random seeds for reproducibility
- Tune too many parameters simultaneously (curse of dimensionality)
```

 8. Resources and Further Learning

 8.1. Documentation
- [Scikit-Learn Grid Search](https://scikit-learn.org/stable/modules/grid_search.html)
- [Optuna Documentation](https://optuna.readthedocs.io/)
- [Hyperopt Documentation](http://hyperopt.github.io/hyperopt/)

 8.2. Key Takeaways
- Grid Search is exhaustive but computationally expensive
- Random Search is more efficient for high-dimensional spaces
- Bayesian Optimization finds optimal parameters with fewer trials
- Always validate tuned models on independent test data
- Consider computational cost vs. performance improvement trade-offs

---

Next Steps: Apply these tuning strategies to your models, experiment with different search algorithms, and systematically optimize model performance.
