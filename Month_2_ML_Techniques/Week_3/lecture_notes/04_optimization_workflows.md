 Optimization Workflows: Systematic Model Improvement

 1. Introduction
Machine learning is an iterative process that requires systematic experimentation, tracking, and improvement. This lecture covers experiment tracking with MLflow, model versioning, continuous optimization workflows, and production deployment pipelines.

 2. Experiment Tracking Fundamentals

 2.1. Why Track Experiments
- Reproducibility: Know exactly what produced each model
- Collaboration: Share results and compare approaches
- Decision Making: Data-driven model selection
- Compliance: Audit trails for regulatory requirements
- Knowledge Management: Build institutional knowledge

 2.2. What to Track
```
Essential Experiment Metadata:

1. PARAMETERS
   - Hyperparameters (learning rate, max_depth, etc.)
   - Model architecture
   - Feature engineering choices
   - Data preprocessing settings

2. METRICS
   - Training metrics (loss, accuracy)
   - Validation metrics (cross-validation scores)
   - Test metrics (final evaluation)
   - Business metrics (ROI, conversion rates)

3. ARTIFACTS
   - Trained models
   - Dataset versions
   - Feature importance plots
   - Confusion matrices

4. CONTEXT
   - Code version (Git commit)
   - Environment (dependencies)
   - Hardware (CPU/GPU specs)
   - Timestamp
```

 3. MLflow Implementation

 3.1. Complete MLflow Workflow
```python
import mlflow
import mlflow.sklearn
from mlflow.tracking import MlflowClient
import pandas as pd
import numpy as np
from sklearn.model_selection import cross_val_score
from sklearn.ensemble import RandomForestClassifier
import logging
import json
from datetime import datetime
import warnings
warnings.filterwarnings('ignore')

class MLflowExperimentManager:
    """Complete MLflow experiment management system"""
    
    def __init__(self, experiment_name="default_experiment", tracking_uri=None):
        self.experiment_name = experiment_name
        self.tracking_uri = tracking_uri or "sqlite:///mlflow.db"
        
         Set up MLflow
        mlflow.set_tracking_uri(self.tracking_uri)
        mlflow.set_experiment(experiment_name)
        
        self.client = MlflowClient()
        self.current_run = None
    
    def start_run(self, run_name=None, nested=False):
        """Start a new MLflow run"""
        self.current_run = mlflow.start_run(run_name=run_name, nested=nested)
        print(f"🚀 Started run: {self.current_run.info.run_id}")
        return self.current_run
    
    def log_parameters(self, params):
        """Log multiple parameters"""
        for key, value in params.items():
            mlflow.log_param(key, value)
        print(f"📝 Logged {len(params)} parameters")
    
    def log_metrics(self, metrics, step=None):
        """Log multiple metrics"""
        for key, value in metrics.items():
            if step is not None:
                mlflow.log_metric(key, value, step=step)
            else:
                mlflow.log_metric(key, value)
        print(f"📊 Logged {len(metrics)} metrics")
    
    def log_model(self, model, model_name, signature=None, input_example=None):
        """Log trained model with optional signature"""
        mlflow.sklearn.log_model(
            model, 
            model_name,
            signature=signature,
            input_example=input_example
        )
        print(f"💾 Logged model: {model_name}")
    
    def log_artifacts(self, artifact_dict):
        """Log artifacts (plots, files, etc.)"""
        for name, path in artifact_dict.items():
            mlflow.log_artifact(path, name)
        print(f"📁 Logged {len(artifact_dict)} artifacts")
    
    def log_dataset_info(self, X_train, X_test, feature_names=None):
        """Log dataset characteristics"""
        dataset_info = {
            'train_samples': X_train.shape[0],
            'test_samples': X_test.shape[0],
            'n_features': X_train.shape[1],
            'feature_names': feature_names or list(range(X_train.shape[1]))
        }
        
         Save as JSON artifact
        with open('dataset_info.json', 'w') as f:
            json.dump(dataset_info, f, indent=2)
        mlflow.log_artifact('dataset_info.json')
        
         Log as params
        mlflow.log_param('train_samples', dataset_info['train_samples'])
        mlflow.log_param('test_samples', dataset_info['test_samples'])
        mlflow.log_param('n_features', dataset_info['n_features'])
    
    def end_run(self, status='FINISHED'):
        """End current run"""
        mlflow.end_run(status=status)
        print(f"✅ Run ended with status: {status}")
    
    def search_runs(self, filter_string="", order_by=None):
        """Search and compare runs"""
        experiment = self.client.get_experiment_by_name(self.experiment_name)
        runs = self.client.search_runs(
            experiment_ids=[experiment.experiment_id],
            filter_string=filter_string,
            order_by=order_by or ["metrics.accuracy DESC"]
        )
        return runs
    
    def get_best_run(self, metric_name='accuracy', mode='DESC'):
        """Get best run based on metric"""
        order_by = [f"metrics.{metric_name} {mode}"]
        runs = self.search_runs(order_by=order_by)
        
        if runs:
            best_run = runs[0]
            print(f"🏆 Best run: {best_run.info.run_id}")
            print(f"   {metric_name}: {best_run.data.metrics.get(metric_name)}")
            return best_run
        return None
    
    def compare_runs(self, metric_names=None):
        """Create comparison dataframe of all runs"""
        runs = self.search_runs()
        
        data = []
        for run in runs:
            run_data = {
                'run_id': run.info.run_id,
                'run_name': run.data.tags.get('mlflow.runName', 'unnamed'),
                'status': run.info.status,
                'start_time': datetime.fromtimestamp(run.info.start_time / 1000)
            }
            
             Add parameters
            run_data.update(run.data.params)
            
             Add metrics
            run_data.update(run.data.metrics)
            
            data.append(run_data)
        
        return pd.DataFrame(data)

 Usage Example
mlflow_manager = MLflowExperimentManager("customer_churn_prediction")

 Start run
mlflow_manager.start_run(run_name="random_forest_v1")

 Log parameters
mlflow_manager.log_parameters({
    'model_type': 'RandomForest',
    'n_estimators': 100,
    'max_depth': 10,
    'min_samples_split': 2
})

 Train model and log
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)
accuracy = model.score(X_test, y_test)

 Log metrics and model
mlflow_manager.log_metrics({'accuracy': accuracy, 'f1_score': 0.85})
mlflow_manager.log_model(model, "random_forest_model")
mlflow_manager.log_dataset_info(X_train, X_test)

 End run
mlflow_manager.end_run()

 Compare all runs
comparison_df = mlflow_manager.compare_runs()
print(comparison_df)
```

 4. Model Registry and Versioning

 4.1. MLflow Model Registry
```python
class MLflowModelRegistry:
    """Manage model versions and deployments"""
    
    def __init__(self, tracking_uri=None):
        self.tracking_uri = tracking_uri or "sqlite:///mlflow.db"
        mlflow.set_tracking_uri(self.tracking_uri)
        self.client = MlflowClient()
    
    def register_model(self, model_uri, model_name, tags=None):
        """Register a new model version"""
        registered_model = mlflow.register_model(model_uri, model_name)
        
        if tags:
            for key, value in tags.items():
                self.client.set_model_version_tag(
                    name=model_name,
                    version=registered_model.version,
                    key=key,
                    value=value
                )
        
        print(f"📦 Registered {model_name} version {registered_model.version}")
        return registered_model
    
    def transition_model_stage(self, model_name, version, stage):
        """Transition model to new stage (Staging/Production/Archived)"""
        self.client.transition_model_version_stage(
            name=model_name,
            version=version,
            stage=stage
        )
        print(f"🔄 Transitioned {model_name} v{version} to {stage}")
    
    def get_production_model(self, model_name):
        """Get the current production model"""
        versions = self.client.get_latest_versions(model_name, stages=["Production"])
        if versions:
            return versions[0]
        return None
    
    def compare_model_versions(self, model_name):
        """Compare all versions of a model"""
        versions = self.client.search_model_versions(f"name='{model_name}'")
        
        comparison = []
        for v in versions:
             Get associated run
            run = self.client.get_run(v.run_id)
            
            comparison.append({
                'version': v.version,
                'stage': v.current_stage,
                'status': v.status,
                'created': datetime.fromtimestamp(v.creation_timestamp / 1000),
                'metrics': run.data.metrics,
                'params': run.data.params
            })
        
        return pd.DataFrame(comparison)

 Usage
registry = MLflowModelRegistry()

 Register model
model_uri = f"runs:/{mlflow_manager.current_run.info.run_id}/random_forest_model"
registered = registry.register_model(
    model_uri, 
    "churn_prediction_model",
    tags={'framework': 'sklearn', 'algorithm': 'random_forest'}
)

 Promote to staging
registry.transition_model_stage("churn_prediction_model", registered.version, "Staging")

 Promote to production after validation
registry.transition_model_stage("churn_prediction_model", registered.version, "Production")
```

 5. Automated Optimization Pipeline

 5.1. End-to-End Optimization Workflow
```python
class AutomatedOptimizationPipeline:
    """Automated ML optimization with experiment tracking"""
    
    def __init__(self, experiment_name="optimization_pipeline"):
        self.mlflow_manager = MLflowExperimentManager(experiment_name)
        self.results_history = []
    
    def optimize_hyperparameters(self, X, y, model_class, param_grid, 
                               cv=5, n_iter=20, optimization_metric='f1_macro'):
        """Run hyperparameter optimization with full tracking"""
        
        from sklearn.model_selection import RandomizedSearchCV
        
        print("🔬 Starting Hyperparameter Optimization")
        print(f"Search space size: {len(param_grid)} parameters")
        
        best_score = 0
        best_params = None
        best_model = None
        
        for i in range(n_iter):
            run_name = f"optimization_iter_{i+1}"
            
             Sample parameters
            params = {k: np.random.choice(v) if isinstance(v, list) else v.sample()[0] 
                     for k, v in param_grid.items()}
            
             Start MLflow run
            self.mlflow_manager.start_run(run_name=run_name, nested=True)
            
            try:
                 Log parameters
                self.mlflow_manager.log_parameters(params)
                
                 Train and evaluate
                model = model_class(params, random_state=42)
                scores = cross_val_score(model, X, y, cv=cv, scoring=optimization_metric)
                
                mean_score = scores.mean()
                std_score = scores.std()
                
                 Log metrics
                self.mlflow_manager.log_metrics({
                    optimization_metric: mean_score,
                    'std_score': std_score,
                    'iteration': i
                })
                
                 Log model if best so far
                if mean_score > best_score:
                    best_score = mean_score
                    best_params = params
                    best_model = model
                    
                    self.mlflow_manager.log_model(model, f"best_model_iter_{i}")
                    print(f"  🌟 New best score: {mean_score:.4f} at iteration {i}")
                
                self.results_history.append({
                    'iteration': i,
                    'params': params,
                    'score': mean_score,
                    'std': std_score
                })
                
            except Exception as e:
                print(f"  ❌ Error in iteration {i}: {e}")
                self.mlflow_manager.end_run(status='FAILED')
                continue
            
            finally:
                self.mlflow_manager.end_run()
        
        print(f"\n🏆 Optimization Complete!")
        print(f"Best score: {best_score:.4f}")
        print(f"Best parameters: {best_params}")
        
        return best_model, best_params, self.results_history
    
    def plot_optimization_history(self):
        """Visualize optimization progress"""
        import matplotlib.pyplot as plt
        
        fig, ax = plt.subplots(figsize=(12, 6))
        
        iterations = [r['iteration'] for r in self.results_history]
        scores = [r['score'] for r in self.results_history]
        
        ax.plot(iterations, scores, 'o-', alpha=0.7)
        ax.set_xlabel('Iteration')
        ax.set_ylabel('Score')
        ax.set_title('Optimization History')
        ax.grid(True, alpha=0.3)
        
         Mark best
        best_idx = np.argmax(scores)
        ax.plot(iterations[best_idx], scores[best_idx], 'r', markersize=15, 
               label=f'Best: {scores[best_idx]:.4f}')
        ax.legend()
        
        return fig

 Usage
optimizer = AutomatedOptimizationPipeline("churn_optimization")
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [5, 10, 15, None],
    'min_samples_split': [2, 5, 10]
}

best_model, best_params, history = optimizer.optimize_hyperparameters(
    X_train, y_train,
    RandomForestClassifier,
    param_grid,
    n_iter=30
)
optimizer.plot_optimization_history()
```

 6. Best Practices Summary

 6.1. Experiment Tracking Guidelines
```
✅ DO:
- Track every experiment, even failed ones
- Use meaningful run names and descriptions
- Log all relevant parameters and metrics
- Version your datasets alongside models
- Set up automated alerts for performance degradation
- Document model decisions and business context
- Use model stages (Staging/Production) properly

❌ DON'T:
- Skip tracking for "quick experiments"
- Log sensitive data (PII) in experiments
- Forget to log random seeds for reproducibility
- Ignore failed runs - they teach valuable lessons
- Let production models exist without version control
- Mix development and production experiments
```

 7. Resources and Further Learning

 7.1. Documentation
- [MLflow Documentation](https://www.mlflow.org/docs/latest/index.html)
- [Weights & Biases Documentation](https://docs.wandb.ai/)
- [DVC (Data Version Control)](https://dvc.org/doc)

 7.2. Key Takeaways
- Experiment tracking is essential for reproducible ML
- MLflow provides end-to-end ML lifecycle management
- Model registry enables proper CI/CD for ML
- Automated optimization accelerates model improvement
- Always version models, data, and code together

---

Next Steps: Set up MLflow for your projects, implement model registries, and build automated optimization pipelines for continuous model improvement.
