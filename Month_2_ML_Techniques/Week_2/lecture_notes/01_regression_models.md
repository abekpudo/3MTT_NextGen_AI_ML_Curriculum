 Regression Models: Predicting Continuous Outcomes

 1. Introduction
Regression analysis is the cornerstone of predictive modeling for continuous outcomes. This lecture covers linear regression, polynomial regression, regularized regression (Ridge, Lasso, Elastic Net), and advanced regression techniques with comprehensive implementations and evaluation strategies.

 2. Linear Regression Fundamentals

 2.1. Mathematical Foundation
```
Linear Regression Model: y = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ + ε

Where:
- y: Target variable (continuous)
- β₀: Intercept (bias term)
- βᵢ: Coefficients (weights) for each feature
- xᵢ: Input features
- ε: Error term (residuals)
```

 2.2. Complete Linear Regression Implementation
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet
from sklearn.preprocessing import PolynomialFeatures, StandardScaler
from sklearn.model_selection import train_test_split, cross_val_score, GridSearchCV
from sklearn.metrics import (
    mean_squared_error, mean_absolute_error, r2_score,
    explained_variance_score
)
from scipy import stats
import warnings
warnings.filterwarnings('ignore')

class ComprehensiveRegression:
    """Complete regression analysis framework"""
    
    def __init__(self, model_type='linear'):
        self.model_type = model_type
        self.model = None
        self.scaler = StandardScaler()
        self.metrics = {}
        self.is_fitted = False
    
    def create_model(self, params):
        """Create regression model with specified parameters"""
        if self.model_type == 'linear':
            self.model = LinearRegression(params)
        elif self.model_type == 'ridge':
            alpha = params.get('alpha', 1.0)
            self.model = Ridge(alpha=alpha)
        elif self.model_type == 'lasso':
            alpha = params.get('alpha', 0.1)
            self.model = Lasso(alpha=alpha)
        elif self.model_type == 'elasticnet':
            alpha = params.get('alpha', 0.1)
            l1_ratio = params.get('l1_ratio', 0.5)
            self.model = ElasticNet(alpha=alpha, l1_ratio=l1_ratio)
        
        return self
    
    def fit(self, X_train, y_train, scale=True):
        """Fit the regression model"""
        if scale:
            X_train_scaled = self.scaler.fit_transform(X_train)
        else:
            X_train_scaled = X_train
        
        self.model.fit(X_train_scaled, y_train)
        self.is_fitted = True
        self.feature_names = X_train.columns if hasattr(X_train, 'columns') else None
        
        return self
    
    def predict(self, X_test, scale=True):
        """Make predictions"""
        if not self.is_fitted:
            raise ValueError("Model must be fitted before prediction")
        
        if scale:
            X_test_scaled = self.scaler.transform(X_test)
        else:
            X_test_scaled = X_test
        
        return self.model.predict(X_test_scaled)
    
    def comprehensive_evaluation(self, X_test, y_test, scale=True):
        """Complete model evaluation with multiple metrics"""
        y_pred = self.predict(X_test, scale=scale)
        
         Basic metrics
        self.metrics = {
            'r2_score': r2_score(y_test, y_pred),
            'adjusted_r2': self._adjusted_r2(y_test, y_pred, X_test.shape[1]),
            'mse': mean_squared_error(y_test, y_pred),
            'rmse': np.sqrt(mean_squared_error(y_test, y_pred)),
            'mae': mean_absolute_error(y_test, y_pred),
            'mape': np.mean(np.abs((y_test - y_pred) / y_test))  100,
            'explained_variance': explained_variance_score(y_test, y_pred),
            'residual_std': np.std(y_test - y_pred)
        }
        
        return self.metrics
    
    def _adjusted_r2(self, y_true, y_pred, n_features):
        """Calculate adjusted R-squared"""
        n = len(y_true)
        r2 = r2_score(y_true, y_pred)
        adj_r2 = 1 - (1 - r2)  (n - 1) / (n - n_features - 1)
        return adj_r2
    
    def get_feature_importance(self):
        """Extract feature importance (coefficients)"""
        if not self.is_fitted:
            raise ValueError("Model must be fitted first")
        
        importance = pd.DataFrame({
            'feature': self.feature_names if self.feature_names else range(len(self.model.coef_)),
            'coefficient': self.model.coef_,
            'abs_coefficient': np.abs(self.model.coef_)
        }).sort_values('abs_coefficient', ascending=False)
        
        return importance

 Usage
reg = ComprehensiveRegression(model_type='linear')
reg.create_model()
reg.fit(X_train, y_train)
metrics = reg.comprehensive_evaluation(X_test, y_test)
importance = reg.get_feature_importance()
print(metrics)
```

 3. Polynomial and Non-Linear Regression

 3.1. Polynomial Features Implementation
```python
class PolynomialRegression:
    """Polynomial regression with automatic degree selection"""
    
    def __init__(self, max_degree=5):
        self.max_degree = max_degree
        self.best_degree = None
        self.poly_features = None
        self.model = None
    
    def find_optimal_degree(self, X, y, degrees=None, cv=5):
        """Find optimal polynomial degree using cross-validation"""
        if degrees is None:
            degrees = range(1, self.max_degree + 1)
        
        cv_scores = []
        
        for degree in degrees:
            poly = PolynomialFeatures(degree=degree, include_bias=False)
            X_poly = poly.fit_transform(X)
            
            model = LinearRegression()
            scores = cross_val_score(model, X_poly, y, cv=cv, 
                                   scoring='neg_mean_squared_error')
            cv_scores.append({
                'degree': degree,
                'mean_score': -scores.mean(),
                'std_score': scores.std()
            })
        
         Find best degree (lowest MSE, but not overfitting)
        cv_df = pd.DataFrame(cv_scores)
        self.best_degree = cv_df.loc[cv_df['mean_score'].idxmin(), 'degree']
        
        return cv_df
    
    def fit(self, X, y, degree=None):
        """Fit polynomial regression"""
        if degree is None:
            degree = self.best_degree or 2
        
        self.poly_features = PolynomialFeatures(degree=degree, include_bias=False)
        X_poly = self.poly_features.fit_transform(X)
        
        self.model = LinearRegression()
        self.model.fit(X_poly, y)
        
        return self
    
    def predict(self, X):
        """Make predictions with polynomial features"""
        X_poly = self.poly_features.transform(X)
        return self.model.predict(X_poly)
    
    def plot_polynomial_fit(self, X, y, feature_idx=0):
        """Visualize polynomial fit for single feature"""
        if X.shape[1] > 1:
            print("Visualization works best with single feature")
            return
        
         Sort for smooth curve
        sort_idx = np.argsort(X[:, 0])
        X_sorted = X[sort_idx]
        y_sorted = y[sort_idx]
        y_pred = self.predict(X_sorted)
        
        plt.figure(figsize=(10, 6))
        plt.scatter(X, y, alpha=0.5, label='Data')
        plt.plot(X_sorted, y_pred, 'r-', label=f'Polynomial (degree={self.poly_features.degree})')
        plt.xlabel('Feature')
        plt.ylabel('Target')
        plt.legend()
        plt.title('Polynomial Regression Fit')
        plt.show()

 Usage
poly_reg = PolynomialRegression(max_degree=5)
cv_results = poly_reg.find_optimal_degree(X, y)
print(f"Optimal degree: {poly_reg.best_degree}")
print(cv_results)

poly_reg.fit(X, y)
poly_reg.plot_polynomial_fit(X, y)
```

 4. Regularized Regression: Ridge, Lasso, Elastic Net

 4.1. Complete Regularization Analysis
```python
class RegularizationAnalysis:
    """Comprehensive regularization analysis and comparison"""
    
    def __init__(self):
        self.models = {}
        self.results = {}
    
    def compare_regularization_methods(self, X_train, X_test, y_train, y_test, 
                                      alphas=np.logspace(-4, 4, 50)):
        """Compare Ridge, Lasso, and Elastic Net across alpha values"""
        
        methods = {
            'Ridge': Ridge,
            'Lasso': Lasso,
            'ElasticNet': lambda a: ElasticNet(alpha=a, l1_ratio=0.5)
        }
        
        for method_name, ModelClass in methods.items():
            train_scores = []
            test_scores = []
            coefficients = []
            
            for alpha in alphas:
                model = ModelClass(alpha) if method_name != 'ElasticNet' else ModelClass(alpha)
                model.fit(X_train, y_train)
                
                train_scores.append(model.score(X_train, y_train))
                test_scores.append(model.score(X_test, y_test))
                coefficients.append(model.coef_)
            
            self.results[method_name] = {
                'alphas': alphas,
                'train_scores': train_scores,
                'test_scores': test_scores,
                'coefficients': coefficients
            }
        
        return self.results
    
    def plot_regularization_paths(self):
        """Visualize regularization paths"""
        fig, axes = plt.subplots(2, 2, figsize=(15, 10))
        
        for idx, (method, results) in enumerate(self.results.items()):
            ax = axes[0, idx] if idx < 2 else axes[1, 0]
            
             Plot scores vs alpha
            ax.semilogx(results['alphas'], results['train_scores'], 
                       label='Train', marker='o', markersize=3)
            ax.semilogx(results['alphas'], results['test_scores'], 
                       label='Test', marker='s', markersize=3)
            ax.set_xlabel('Alpha (Regularization Strength)')
            ax.set_ylabel('R² Score')
            ax.set_title(f'{method} - Performance vs Regularization')
            ax.legend()
            ax.grid(True, alpha=0.3)
        
         Coefficient paths
        ax_coef = axes[1, 1]
        for method, results in self.results.items():
            coefs = np.array(results['coefficients'])
            for i in range(min(5, coefs.shape[1])):   Plot first 5 coefficients
                ax_coef.semilogx(results['alphas'], coefs[:, i], 
                               label=f'{method} - Coef {i}', alpha=0.7)
        
        ax_coef.set_xlabel('Alpha')
        ax_coef.set_ylabel('Coefficient Value')
        ax_coef.set_title('Coefficient Paths')
        ax_coef.legend(bbox_to_anchor=(1.05, 1), loc='upper left')
        ax_coef.grid(True, alpha=0.3)
        
        plt.tight_layout()
        return fig
    
    def find_optimal_alpha(self, X, y, method='ridge', cv=5):
        """Find optimal alpha using cross-validation"""
        from sklearn.linear_model import RidgeCV, LassoCV, ElasticNetCV
        
        alphas = np.logspace(-4, 4, 100)
        
        if method == 'ridge':
            model = RidgeCV(alphas=alphas, cv=cv)
        elif method == 'lasso':
            model = LassoCV(alphas=alphas, cv=cv, random_state=42)
        elif method == 'elasticnet':
            model = ElasticNetCV(alphas=alphas, cv=cv, random_state=42)
        
        model.fit(X, y)
        
        return {
            'optimal_alpha': model.alpha_,
            'best_score': model.score(X, y),
            'cv_results': model
        }

 Usage
reg_analysis = RegularizationAnalysis()
results = reg_analysis.compare_regularization_methods(
    X_train, X_test, y_train, y_test
)
fig = reg_analysis.plot_regularization_paths()

optimal_ridge = reg_analysis.find_optimal_alpha(X, y, method='ridge')
print(f"Optimal alpha for Ridge: {optimal_ridge['optimal_alpha']}")
```

 5. Residual Analysis and Diagnostics

 5.1. Comprehensive Residual Analysis
```python
class ResidualDiagnostics:
    """Complete residual analysis for regression diagnostics"""
    
    def __init__(self, model, X_test, y_test):
        self.model = model
        self.X_test = X_test
        self.y_test = y_test
        self.y_pred = model.predict(X_test)
        self.residuals = y_test - self.y_pred
    
    def comprehensive_analysis(self):
        """Run all diagnostic tests"""
        diagnostics = {
            'normality': self.test_normality(),
            'homoscedasticity': self.test_homoscedasticity(),
            'autocorrelation': self.test_autocorrelation(),
            'influential_points': self.identify_influential_points()
        }
        
        return diagnostics
    
    def test_normality(self):
        """Test if residuals are normally distributed"""
         Shapiro-Wilk test
        stat, p_value = stats.shapiro(self.residuals[:min(5000, len(self.residuals))])
        
        return {
            'test': 'Shapiro-Wilk',
            'statistic': stat,
            'p_value': p_value,
            'normal': p_value > 0.05
        }
    
    def test_homoscedasticity(self):
        """Test for constant variance of residuals"""
         Breusch-Pagan test approximation
        from scipy.stats import pearsonr
        
        correlation, p_value = pearsonr(np.abs(self.residuals), self.y_pred)
        
        return {
            'correlation': correlation,
            'p_value': p_value,
            'homoscedastic': p_value > 0.05
        }
    
    def test_autocorrelation(self):
        """Test for autocorrelation in residuals"""
        from statsmodels.stats.stattools import durbin_watson
        
        dw_stat = durbin_watson(self.residuals)
        
         DW ≈ 2: no autocorrelation
         DW < 2: positive autocorrelation
         DW > 2: negative autocorrelation
        
        return {
            'durbin_watson': dw_stat,
            'autocorrelation': 'none' if 1.5 < dw_stat < 2.5 else 'present'
        }
    
    def identify_influential_points(self):
        """Identify potentially influential data points"""
         Standardized residuals
        standardized_residuals = self.residuals / np.std(self.residuals)
        
         Cook's distance approximation
        leverage = self._calculate_leverage()
        cooks_d = (standardized_residuals  2)  leverage / (1 - leverage)
        
        influential_threshold = 4 / len(self.y_test)
        influential_points = np.where(cooks_d > influential_threshold)[0]
        
        return {
            'influential_indices': influential_points,
            'threshold': influential_threshold,
            'cooks_distances': cooks_d
        }
    
    def _calculate_leverage(self):
        """Calculate leverage (hat values)"""
        X = np.column_stack([np.ones(len(self.X_test)), self.X_test])
        hat_matrix = X @ np.linalg.inv(X.T @ X) @ X.T
        return np.diag(hat_matrix)
    
    def plot_diagnostics(self):
        """Create comprehensive diagnostic plots"""
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        
         1. Residuals vs Fitted
        axes[0, 0].scatter(self.y_pred, self.residuals, alpha=0.6)
        axes[0, 0].axhline(y=0, color='r', linestyle='--')
        axes[0, 0].set_xlabel('Fitted Values')
        axes[0, 0].set_ylabel('Residuals')
        axes[0, 0].set_title('Residuals vs Fitted')
        axes[0, 0].grid(True, alpha=0.3)
        
         2. Q-Q Plot
        stats.probplot(self.residuals, dist="norm", plot=axes[0, 1])
        axes[0, 1].set_title('Normal Q-Q Plot')
        axes[0, 1].grid(True, alpha=0.3)
        
         3. Scale-Location (sqrt of standardized residuals vs fitted)
        standardized_residuals = self.residuals / np.std(self.residuals)
        axes[1, 0].scatter(self.y_pred, np.sqrt(np.abs(standardized_residuals)), alpha=0.6)
        axes[1, 0].set_xlabel('Fitted Values')
        axes[1, 0].set_ylabel('√|Standardized Residuals|')
        axes[1, 0].set_title('Scale-Location Plot')
        axes[1, 0].grid(True, alpha=0.3)
        
         4. Residuals Distribution
        axes[1, 1].hist(self.residuals, bins=30, edgecolor='black', alpha=0.7)
        axes[1, 1].set_xlabel('Residuals')
        axes[1, 1].set_ylabel('Frequency')
        axes[1, 1].set_title('Residuals Distribution')
        axes[1, 1].grid(True, alpha=0.3)
        
        plt.tight_layout()
        return fig

 Usage
diagnostics = ResidualDiagnostics(reg.model, X_test, y_test)
diag_results = diagnostics.comprehensive_analysis()
fig = diagnostics.plot_diagnostics()
print(diag_results)
```

 6. Advanced Regression Techniques

 6.1. Robust Regression (Handling Outliers)
```python
from sklearn.linear_model import HuberRegressor, RANSACRegressor

def robust_regression_comparison(X, y, outlier_fraction=0.1):
    """Compare robust regression methods"""
    
     Add artificial outliers
    n_outliers = int(len(y)  outlier_fraction)
    outlier_idx = np.random.choice(len(y), n_outliers, replace=False)
    y_outliers = y.copy()
    y_outliers[outlier_idx] += np.random.normal(0, 10, n_outliers)
    
    models = {
        'Standard Linear': LinearRegression(),
        'Huber Regression': HuberRegressor(epsilon=1.35),
        'RANSAC': RANSACRegressor(random_state=42)
    }
    
    results = {}
    for name, model in models.items():
        model.fit(X, y_outliers)
        y_pred = model.predict(X)
        
        results[name] = {
            'r2': r2_score(y_outliers, y_pred),
            'rmse': np.sqrt(mean_squared_error(y_outliers, y_pred)),
            'coefficients': model.coef_ if hasattr(model, 'coef_') else model.estimator_.coef_
        }
    
    return results
```

 7. Model Comparison and Selection

 7.1. Comprehensive Model Comparison
```python
class RegressionModelComparison:
    """Compare multiple regression models systematically"""
    
    def __init__(self):
        self.models = {}
        self.results = pd.DataFrame()
    
    def add_model(self, name, model):
        """Add model to comparison"""
        self.models[name] = model
    
    def compare_all(self, X_train, X_test, y_train, y_test, cv=5):
        """Compare all models with cross-validation"""
        comparison_results = []
        
        for name, model in self.models.items():
             Fit model
            model.fit(X_train, y_train)
            
             Cross-validation scores
            cv_scores = cross_val_score(model, X_train, y_train, cv=cv, 
                                       scoring='neg_mean_squared_error')
            
             Test set performance
            y_pred = model.predict(X_test)
            
            comparison_results.append({
                'Model': name,
                'CV_RMSE_Mean': np.sqrt(-cv_scores.mean()),
                'CV_RMSE_Std': np.sqrt(cv_scores.std()),
                'Test_RMSE': np.sqrt(mean_squared_error(y_test, y_pred)),
                'Test_MAE': mean_absolute_error(y_test, y_pred),
                'Test_R2': r2_score(y_test, y_pred),
                'Training_Time': self._measure_training_time(model, X_train, y_train)
            })
        
        self.results = pd.DataFrame(comparison_results)
        return self.results.sort_values('Test_RMSE')
    
    def _measure_training_time(self, model, X, y):
        """Measure model training time"""
        import time
        start = time.time()
        model.fit(X, y)
        return time.time() - start
    
    def plot_comparison(self):
        """Visualize model comparison"""
        if self.results.empty:
            raise ValueError("Run compare_all() first")
        
        fig, axes = plt.subplots(2, 2, figsize=(15, 10))
        
         RMSE Comparison
        self.results.plot(x='Model', y=['CV_RMSE_Mean', 'Test_RMSE'], 
                         kind='bar', ax=axes[0, 0])
        axes[0, 0].set_title('RMSE Comparison')
        axes[0, 0].legend()
        
         R² Comparison
        self.results.plot(x='Model', y='Test_R2', kind='bar', ax=axes[0, 1], color='green')
        axes[0, 1].set_title('R² Score Comparison')
        axes[0, 1].set_ylim(0, 1)
        
         Training Time
        self.results.plot(x='Model', y='Training_Time', kind='bar', ax=axes[1, 0], color='orange')
        axes[1, 0].set_title('Training Time (seconds)')
        
         Metric summary table
        axes[1, 1].axis('off')
        table_data = self.results.round(4).values.tolist()
        table = axes[1, 1].table(cellText=table_data,
                                colLabels=self.results.columns,
                                loc='center',
                                cellLoc='center')
        table.auto_set_font_size(False)
        table.set_fontsize(9)
        axes[1, 1].set_title('Detailed Results')
        
        plt.tight_layout()
        return fig

 Usage
comparison = RegressionModelComparison()
comparison.add_model('Linear', LinearRegression())
comparison.add_model('Ridge', Ridge(alpha=1.0))
comparison.add_model('Lasso', Lasso(alpha=0.1))
comparison.add_model('ElasticNet', ElasticNet(alpha=0.1, l1_ratio=0.5))

results = comparison.compare_all(X_train, X_test, y_train, y_test)
print(results)
fig = comparison.plot_comparison()
```

 8. Best Practices Summary

 8.1. Regression Guidelines
```
✅ DO:
- Always check regression assumptions (linearity, normality, homoscedasticity)
- Use regularization to prevent overfitting
- Perform residual analysis to diagnose model issues
- Scale features for regularized regression
- Use cross-validation for reliable performance estimates
- Compare multiple models systematically

❌ DON'T:
- Ignore outliers without investigation
- Use polynomial features without regularization
- Skip residual diagnostics
- Forget to scale features for distance-based methods
- Use test set for model selection
- Ignore multicollinearity in linear regression
```

 9. Resources and Further Learning

 9.1. Documentation
- [Scikit-Learn Linear Models](https://scikit-learn.org/stable/modules/linear_model.html)
- [Statsmodels Regression Diagnostics](https://www.statsmodels.org/stable/diagnostic.html)
- [ISLR - Introduction to Statistical Learning](https://www.statlearning.com/)

 9.2. Key Takeaways
- Linear regression is the foundation but has strict assumptions
- Regularization prevents overfitting and handles multicollinearity
- Residual analysis is crucial for model validation
- Polynomial regression adds flexibility but increases complexity
- Always validate model assumptions before deploying

---

Next Steps: Apply these regression techniques to real datasets, practice residual analysis, and experiment with different regularization strengths.
