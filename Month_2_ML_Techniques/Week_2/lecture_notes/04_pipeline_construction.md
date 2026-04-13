 Pipeline Construction: From Data to Production

 1. Introduction
Scikit-Learn pipelines are essential for building robust, reproducible machine learning workflows. This lecture covers comprehensive pipeline construction, including preprocessing, feature engineering, model training, hyperparameter tuning, and production deployment with proper serialization and monitoring.

 2. Why Pipelines Matter
- Data Leakage Prevention: Ensures transformers fit only on training data
- Reproducibility: Consistent transformations across training and inference
- Code Organization: Encapsulates entire ML workflow in single object
- Hyperparameter Tuning: Grid search across preprocessing and model parameters
- Production Readiness: Easy serialization and deployment

 3. Pipeline Architecture

 3.1. Complete Pipeline Structure
```
┌─────────────────────────────────────────────────────────────────┐
│                    ML Pipeline Architecture                      │
├─────────────────────────────────────────────────────────────────┤
│ 1. Data Input → Raw features (numerical, categorical, text)       │
│ 2. Preprocessing → Imputation, scaling, encoding                   │
│ 3. Feature Engineering → Transformations, interactions              │
│ 4. Feature Selection → Remove redundant/irrelevant features       │
│ 5. Model Training → Algorithm fitting                            │
│ 6. Post-processing → Calibration, threshold tuning                │
│ 7. Prediction Output → Final predictions                         │
└─────────────────────────────────────────────────────────────────┘
```

 4. Comprehensive Pipeline Implementation

 4.1. Advanced Pipeline Builder
```python
import pandas as pd
import numpy as np
from sklearn.pipeline import Pipeline, FeatureUnion
from sklearn.compose import ColumnTransformer, make_column_selector
from sklearn.preprocessing import (
    StandardScaler, MinMaxScaler, RobustScaler,
    OneHotEncoder, OrdinalEncoder, LabelEncoder,
    PolynomialFeatures, PowerTransformer
)
from sklearn.impute import SimpleImputer, KNNImputer
from sklearn.feature_selection import SelectKBest, f_classif, mutual_info_classif
from sklearn.decomposition import PCA
from sklearn.base import BaseEstimator, TransformerMixin
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from sklearn.metrics import classification_report
import joblib
import warnings
warnings.filterwarnings('ignore')

 Custom Transformers
class OutlierClipper(BaseEstimator, TransformerMixin):
    """Custom transformer to clip outliers"""
    
    def __init__(self, lower_quantile=0.01, upper_quantile=0.99):
        self.lower_quantile = lower_quantile
        self.upper_quantile = upper_quantile
        self.bounds_ = {}
    
    def fit(self, X, y=None):
        for col in range(X.shape[1]):
            self.bounds_[col] = (
                np.quantile(X[:, col], self.lower_quantile),
                np.quantile(X[:, col], self.upper_quantile)
            )
        return self
    
    def transform(self, X):
        X_transformed = X.copy()
        for col, (lower, upper) in self.bounds_.items():
            X_transformed[:, col] = np.clip(X[:, col], lower, upper)
        return X_transformed

class RareCategoryEncoder(BaseEstimator, TransformerMixin):
    """Encode rare categories as 'Other'"""
    
    def __init__(self, threshold=0.01):
        self.threshold = threshold
        self.freq_maps_ = {}
    
    def fit(self, X, y=None):
        for col in range(X.shape[1]):
            value_counts = pd.Series(X[:, col]).value_counts(normalize=True)
            self.freq_maps_[col] = set(value_counts[value_counts >= self.threshold].index)
        return self
    
    def transform(self, X):
        X_transformed = X.copy()
        for col, valid_categories in self.freq_maps_.items():
            X_transformed[:, col] = [
                x if x in valid_categories else 'Other' 
                for x in X[:, col]
            ]
        return X_transformed

class ComprehensiveMLPipeline:
    """Complete ML pipeline builder with all preprocessing steps"""
    
    def __init__(self, model_type='random_forest', problem_type='classification'):
        self.model_type = model_type
        self.problem_type = problem_type
        self.pipeline = None
        self.preprocessor = None
        self.feature_names = None
        self.is_fitted = False
    
    def build_pipeline(self, 
                       numerical_features=None,
                       categorical_features=None,
                       text_features=None,
                       imputation_strategy='median',
                       scaling_method='standard',
                       handle_outliers=True,
                       feature_selection=None,
                       dimensionality_reduction=None):
        """
        Build comprehensive ML pipeline
        
        Parameters:
        - numerical_features: List of numerical column names
        - categorical_features: List of categorical column names
        - text_features: List of text column names
        - imputation_strategy: 'mean', 'median', 'most_frequent', 'knn'
        - scaling_method: 'standard', 'minmax', 'robust', 'power'
        - handle_outliers: Whether to clip outliers
        - feature_selection: 'k_best', 'mutual_info', or None
        - dimensionality_reduction: 'pca' or None
        """
        
        transformers = []
        
         1. Numerical Pipeline
        if numerical_features:
            num_steps = []
            
             Imputation
            if imputation_strategy == 'knn':
                num_steps.append(('imputer', KNNImputer(n_neighbors=5)))
            else:
                num_steps.append(('imputer', SimpleImputer(strategy=imputation_strategy)))
            
             Outlier handling
            if handle_outliers:
                num_steps.append(('outlier_clipper', OutlierClipper()))
            
             Scaling
            if scaling_method == 'standard':
                num_steps.append(('scaler', StandardScaler()))
            elif scaling_method == 'minmax':
                num_steps.append(('scaler', MinMaxScaler()))
            elif scaling_method == 'robust':
                num_steps.append(('scaler', RobustScaler()))
            elif scaling_method == 'power':
                num_steps.append(('scaler', PowerTransformer()))
            
            num_pipeline = Pipeline(steps=num_steps)
            transformers.append(('num', num_pipeline, numerical_features))
        
         2. Categorical Pipeline
        if categorical_features:
            cat_steps = [
                ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
                ('rare_encoder', RareCategoryEncoder(threshold=0.01)),
                ('encoder', OneHotEncoder(handle_unknown='ignore', sparse_output=False))
            ]
            
            cat_pipeline = Pipeline(steps=cat_steps)
            transformers.append(('cat', cat_pipeline, categorical_features))
        
         3. Create Preprocessor
        self.preprocessor = ColumnTransformer(
            transformers=transformers,
            remainder='drop',   Drop columns not specified
            verbose_feature_names_out=False
        )
        
         4. Build Final Pipeline
        pipeline_steps = [('preprocessor', self.preprocessor)]
        
         Feature Selection
        if feature_selection == 'k_best':
            score_func = f_classif if self.problem_type == 'classification' else f_regression
            pipeline_steps.append(('feature_selection', SelectKBest(score_func, k='all')))
        elif feature_selection == 'mutual_info':
            score_func = mutual_info_classif if self.problem_type == 'classification' else mutual_info_regression
            pipeline_steps.append(('feature_selection', SelectKBest(score_func, k='all')))
        
         Dimensionality Reduction
        if dimensionality_reduction == 'pca':
            pipeline_steps.append(('pca', PCA(n_components=0.95)))
        
         Model
        model = self._get_model()
        pipeline_steps.append(('model', model))
        
        self.pipeline = Pipeline(steps=pipeline_steps)
        
        return self
    
    def _get_model(self):
        """Get model based on type"""
        models = {
            'logistic': LogisticRegression(max_iter=1000, random_state=42),
            'random_forest': RandomForestClassifier(n_estimators=100, random_state=42),
            'gradient_boosting': GradientBoostingClassifier(random_state=42),
            'svm': SVC(probability=True, random_state=42)
        }
        
        return models.get(self.model_type, RandomForestClassifier(random_state=42))
    
    def fit(self, X, y):
        """Fit the pipeline"""
        self.pipeline.fit(X, y)
        self.is_fitted = True
        
         Store feature names if available
        try:
            self.feature_names = self.pipeline.named_steps['preprocessor'].get_feature_names_out()
        except:
            self.feature_names = None
        
        return self
    
    def predict(self, X):
        """Make predictions"""
        if not self.is_fitted:
            raise ValueError("Pipeline must be fitted first")
        return self.pipeline.predict(X)
    
    def predict_proba(self, X):
        """Get prediction probabilities"""
        if not self.is_fitted:
            raise ValueError("Pipeline must be fitted first")
        if hasattr(self.pipeline.named_steps['model'], 'predict_proba'):
            return self.pipeline.predict_proba(X)
        return None
    
    def get_feature_importance(self):
        """Get feature importance if available"""
        model = self.pipeline.named_steps['model']
        
        if hasattr(model, 'feature_importances_'):
            importance = model.feature_importances_
        elif hasattr(model, 'coef_'):
            importance = np.abs(model.coef_).flatten()
        else:
            return None
        
        if self.feature_names is not None:
            return pd.DataFrame({
                'feature': self.feature_names,
                'importance': importance
            }).sort_values('importance', ascending=False)
        
        return importance

 Usage Example
pipeline_builder = ComprehensiveMLPipeline(
    model_type='random_forest',
    problem_type='classification'
)

pipeline = pipeline_builder.build_pipeline(
    numerical_features=['age', 'income', 'credit_score'],
    categorical_features=['gender', 'occupation', 'city'],
    imputation_strategy='median',
    scaling_method='standard',
    handle_outliers=True,
    feature_selection='k_best'
)

pipeline_builder.fit(X_train, y_train)
predictions = pipeline_builder.predict(X_test)
importance = pipeline_builder.get_feature_importance()
```

 5. Hyperparameter Tuning with Pipelines

 5.1. Grid Search Integration
```python
class PipelineOptimizer:
    """Optimize pipeline hyperparameters"""
    
    def __init__(self, pipeline_builder):
        self.pipeline_builder = pipeline_builder
        self.best_pipeline = None
        self.cv_results = None
    
    def optimize(self, X, y, param_grid, cv=5, scoring='f1_macro', search_type='grid'):
        """
        Run hyperparameter optimization
        
        param_grid example:
        {
            'preprocessor__num__imputer__strategy': ['mean', 'median'],
            'preprocessor__num__scaler': [StandardScaler(), MinMaxScaler()],
            'model__n_estimators': [50, 100, 200],
            'model__max_depth': [5, 10, 15, None]
        }
        """
        
         Build base pipeline
        pipeline = self.pipeline_builder.build_pipeline().pipeline
        
         Choose search strategy
        if search_type == 'grid':
            search = GridSearchCV(
                pipeline, param_grid,
                cv=cv, scoring=scoring,
                n_jobs=-1, verbose=1,
                return_train_score=True
            )
        else:   random
            from scipy.stats import randint, uniform
            search = RandomizedSearchCV(
                pipeline, param_grid,
                n_iter=20, cv=cv, scoring=scoring,
                n_jobs=-1, verbose=1, random_state=42
            )
        
         Fit search
        search.fit(X, y)
        
        self.best_pipeline = search.best_estimator_
        self.cv_results = pd.DataFrame(search.cv_results_)
        
        print(f"Best Score: {search.best_score_:.4f}")
        print(f"Best Parameters: {search.best_params_}")
        
        return self.best_pipeline, self.cv_results
    
    def plot_optimization_results(self, param_name):
        """Plot CV results for a specific parameter"""
        if self.cv_results is None:
            raise ValueError("Run optimize() first")
        
        fig, axes = plt.subplots(1, 2, figsize=(15, 5))
        
         Mean test score vs parameter
        param_values = self.cv_results[f'param_{param_name}']
        mean_scores = self.cv_results['mean_test_score']
        std_scores = self.cv_results['std_test_score']
        
        axes[0].plot(param_values, mean_scores, 'o-', color='blue')
        axes[0].fill_between(param_values, 
                            mean_scores - std_scores,
                            mean_scores + std_scores,
                            alpha=0.3, color='blue')
        axes[0].set_xlabel(param_name)
        axes[0].set_ylabel('Mean Test Score')
        axes[0].set_title(f'CV Scores vs {param_name}')
        axes[0].grid(True, alpha=0.3)
        
         Heatmap of top parameter combinations
        pivot_table = self.cv_results.pivot_table(
            values='mean_test_score',
            index='param_model__max_depth',
            columns='param_model__n_estimators'
        )
        sns.heatmap(pivot_table, annot=True, fmt='.3f', ax=axes[1], cmap='viridis')
        axes[1].set_title('Score Heatmap')
        
        plt.tight_layout()
        return fig

 Usage
optimizer = PipelineOptimizer(pipeline_builder)
best_pipe, results = optimizer.optimize(
    X_train, y_train,
    param_grid={
        'preprocessor__num__imputer__strategy': ['mean', 'median'],
        'model__n_estimators': [50, 100, 200],
        'model__max_depth': [5, 10, 15, None]
    }
)
```

 6. Production Deployment

 6.1. Pipeline Serialization and Loading
```python
class PipelineDeployer:
    """Handle pipeline serialization and deployment"""
    
    def __init__(self, pipeline):
        self.pipeline = pipeline
    
    def save_pipeline(self, filepath, include_metadata=True):
        """
        Save pipeline to disk with optional metadata
        """
        import json
        from datetime import datetime
        
         Save pipeline
        joblib.dump(self.pipeline, filepath)
        
        if include_metadata:
            metadata = {
                'saved_at': datetime.now().isoformat(),
                'pipeline_steps': [step[0] for step in self.pipeline.steps],
                'model_type': type(self.pipeline.named_steps['model']).__name__,
                'scikit_learn_version': sklearn.__version__
            }
            
            metadata_path = filepath.replace('.pkl', '_metadata.json')
            with open(metadata_path, 'w') as f:
                json.dump(metadata, f, indent=2)
        
        print(f"✓ Pipeline saved to {filepath}")
    
    @staticmethod
    def load_pipeline(filepath):
        """Load pipeline from disk"""
        pipeline = joblib.load(filepath)
        print(f"✓ Pipeline loaded from {filepath}")
        return pipeline
    
    def validate_pipeline(self, X_sample, y_sample):
        """Validate pipeline works correctly"""
        try:
             Test prediction
            y_pred = self.pipeline.predict(X_sample)
            
             Test probability prediction if available
            if hasattr(self.pipeline.named_steps['model'], 'predict_proba'):
                y_prob = self.pipeline.predict_proba(X_sample)
            
            print("✓ Pipeline validation successful")
            return True
        except Exception as e:
            print(f"✗ Pipeline validation failed: {e}")
            return False

 Usage
deployer = PipelineDeployer(pipeline_builder.pipeline)
deployer.save_pipeline('production_model.pkl')
deployer.validate_pipeline(X_test.sample(10), y_test.sample(10))

 Load in production
loaded_pipeline = PipelineDeployer.load_pipeline('production_model.pkl')
predictions = loaded_pipeline.predict(new_data)
```

 6.2. API Deployment with FastAPI
```python
 production_api.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import pandas as pd
import joblib

app = FastAPI(title="ML Prediction API", version="1.0")

 Load pipeline on startup
pipeline = joblib.load('production_model.pkl')

class PredictionInput(BaseModel):
    age: float
    income: float
    credit_score: float
    gender: str
    occupation: str
    city: str

class PredictionOutput(BaseModel):
    prediction: int
    probability: float
    confidence: str

@app.post("/predict", response_model=PredictionOutput)
def predict(input_data: PredictionInput):
    try:
         Convert to DataFrame
        data = pd.DataFrame([input_data.dict()])
        
         Make prediction
        prediction = pipeline.predict(data)[0]
        probability = pipeline.predict_proba(data)[0].max()
        
         Determine confidence level
        if probability >= 0.9:
            confidence = "High"
        elif probability >= 0.7:
            confidence = "Medium"
        else:
            confidence = "Low"
        
        return PredictionOutput(
            prediction=int(prediction),
            probability=float(probability),
            confidence=confidence
        )
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/health")
def health_check():
    return {"status": "healthy", "pipeline_loaded": pipeline is not None}
```

 7. Pipeline Monitoring and Maintenance

 7.1. Data Drift Detection
```python
class PipelineMonitor:
    """Monitor pipeline performance and data drift"""
    
    def __init__(self, reference_data):
        self.reference_data = reference_data
        self.reference_stats = self._calculate_stats(reference_data)
    
    def _calculate_stats(self, data):
        """Calculate reference statistics"""
        stats = {}
        for col in data.select_dtypes(include=[np.number]).columns:
            stats[col] = {
                'mean': data[col].mean(),
                'std': data[col].std(),
                'quantiles': data[col].quantile([0.25, 0.5, 0.75]).to_dict()
            }
        return stats
    
    def detect_drift(self, new_data, threshold=3.0):
        """Detect data drift using statistical tests"""
        drift_report = {}
        
        for col in self.reference_stats.keys():
            if col in new_data.columns:
                ref_mean = self.reference_stats[col]['mean']
                ref_std = self.reference_stats[col]['std']
                
                new_mean = new_data[col].mean()
                new_std = new_data[col].std()
                
                 Z-score for mean shift
                z_score = abs(new_mean - ref_mean) / ref_std if ref_std > 0 else 0
                
                drift_report[col] = {
                    'mean_shift_zscore': z_score,
                    'drift_detected': z_score > threshold,
                    'ref_mean': ref_mean,
                    'new_mean': new_mean
                }
        
        return drift_report

 Usage
monitor = PipelineMonitor(X_train)
drift_report = monitor.detect_drift(new_production_data)
print(drift_report)
```

 8. Best Practices Summary

 8.1. Pipeline Guidelines
```
✅ DO:
- Use pipelines to prevent data leakage
- Include all preprocessing in the pipeline
- Use ColumnTransformer for different feature types
- Save complete pipeline (not just model) for deployment
- Version control your pipelines
- Validate pipelines before production deployment
- Monitor for data drift in production

❌ DON'T:
- Fit transformers on entire dataset before splitting
- Forget to handle unknown categories in production
- Skip pipeline validation before deployment
- Ignore feature name tracking
- Use different preprocessing for train and test
- Forget to set random states for reproducibility
```

 9. Resources and Further Learning

 9.1. Documentation
- [Scikit-Learn Pipelines](https://scikit-learn.org/stable/modules/compose.html)
- [Scikit-Learn ColumnTransformer](https://scikit-learn.org/stable/modules/compose.htmlcolumntransformer)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

 9.2. Key Takeaways
- Pipelines are essential for production ML systems
- Always include preprocessing in pipelines to prevent leakage
- Use custom transformers for domain-specific needs
- Version and monitor pipelines in production
- Validate thoroughly before deployment

---

Next Steps: Build complete pipelines for your projects, practice deployment to production environments, and implement monitoring systems.
