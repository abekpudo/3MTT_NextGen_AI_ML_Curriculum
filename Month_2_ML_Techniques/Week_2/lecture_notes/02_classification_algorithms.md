 Classification Algorithms: Predicting Categories

 1. Introduction
Classification is a fundamental supervised learning task where the goal is to predict discrete class labels. This lecture covers core classification algorithms including Logistic Regression, Decision Trees, Random Forests, SVM, and ensemble methods with comprehensive implementations and evaluation strategies for both binary and multi-class problems.

 2. Classification Fundamentals

 2.1. Types of Classification
- Binary Classification: Two classes (e.g., spam/not spam, churn/no churn)
- Multi-class Classification: More than two classes (e.g., animal species, product categories)
- Multi-label Classification: Multiple labels per sample (e.g., tags on an article)
- Imbalanced Classification: Unequal class distribution (common in fraud detection, disease diagnosis)

 2.2. Mathematical Foundation
```
Binary Classification Decision Rule:
    Predict class 1 if P(y=1|X) >= 0.5
    Predict class 0 otherwise

Logistic Regression (Binary):
    P(y=1|X) = 1 / (1 + exp(-z))
    where z = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ

Multi-class Extension (Softmax):
    P(y=k|X) = exp(zₖ) / Σ exp(zⱼ)
    where zₖ = βₖ · X for each class k
```

 3. Complete Classification Implementation

 3.1. Comprehensive Classification Framework
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier, AdaBoostClassifier
from sklearn.svm import SVC
from sklearn.naive_bayes import GaussianNB
from sklearn.neighbors import KNeighborsClassifier
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.model_selection import train_test_split, cross_val_score, GridSearchCV, StratifiedKFold
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    roc_auc_score, confusion_matrix, classification_report,
    precision_recall_curve, roc_curve, average_precision_score,
    log_loss, matthews_corrcoef
)
from sklearn.calibration import CalibratedClassifierCV, calibration_curve
from imblearn.over_sampling import SMOTE, ADASYN
from imblearn.under_sampling import RandomUnderSampler
import warnings
warnings.filterwarnings('ignore')

class ComprehensiveClassifier:
    """Complete classification framework"""
    
    def __init__(self, model_type='logistic', class_weight='balanced'):
        self.model_type = model_type
        self.class_weight = class_weight
        self.model = None
        self.scaler = StandardScaler()
        self.label_encoder = LabelEncoder()
        self.is_fitted = False
        self.classes_ = None
    
    def create_model(self, params):
        """Create classifier with specified parameters"""
        model_map = {
            'logistic': LogisticRegression,
            'decision_tree': DecisionTreeClassifier,
            'random_forest': RandomForestClassifier,
            'gradient_boosting': GradientBoostingClassifier,
            'svm': SVC,
            'naive_bayes': GaussianNB,
            'knn': KNeighborsClassifier,
            'adaboost': AdaBoostClassifier
        }
        
        ModelClass = model_map.get(self.model_type)
        if ModelClass is None:
            raise ValueError(f"Unknown model type: {self.model_type}")
        
         Add class_weight for supported models
        if self.model_type in ['logistic', 'decision_tree', 'random_forest', 'svm']:
            params['class_weight'] = self.class_weight
        
        self.model = ModelClass(params)
        return self
    
    def fit(self, X_train, y_train, scale=True):
        """Fit the classifier"""
         Handle multi-class labels
        if len(np.unique(y_train)) > 2:
            y_train_encoded = self.label_encoder.fit_transform(y_train)
            self.classes_ = self.label_encoder.classes_
        else:
            y_train_encoded = y_train
            self.classes_ = np.unique(y_train)
        
        if scale and self.model_type not in ['naive_bayes', 'decision_tree', 'random_forest']:
            X_train_scaled = self.scaler.fit_transform(X_train)
        else:
            X_train_scaled = X_train
        
        self.model.fit(X_train_scaled, y_train_encoded)
        self.is_fitted = True
        
        return self
    
    def predict(self, X_test, scale=True):
        """Make predictions"""
        if not self.is_fitted:
            raise ValueError("Model must be fitted before prediction")
        
        if scale and self.model_type not in ['naive_bayes', 'decision_tree', 'random_forest']:
            X_test_scaled = self.scaler.transform(X_test)
        else:
            X_test_scaled = X_test
        
        y_pred = self.model.predict(X_test_scaled)
        
         Decode if multi-class
        if len(self.classes_) > 2:
            y_pred = self.label_encoder.inverse_transform(y_pred)
        
        return y_pred
    
    def predict_proba(self, X_test, scale=True):
        """Get prediction probabilities"""
        if not hasattr(self.model, 'predict_proba'):
            raise AttributeError(f"{self.model_type} doesn't support predict_proba")
        
        if scale and self.model_type not in ['naive_bayes', 'decision_tree', 'random_forest']:
            X_test_scaled = self.scaler.transform(X_test)
        else:
            X_test_scaled = X_test
        
        return self.model.predict_proba(X_test_scaled)
    
    def comprehensive_evaluation(self, X_test, y_test, scale=True):
        """Complete model evaluation"""
        y_pred = self.predict(X_test, scale=scale)
        
         Handle encoding
        if len(self.classes_) > 2:
            y_test_encoded = self.label_encoder.transform(y_test)
        else:
            y_test_encoded = y_test
        
        metrics = {
            'accuracy': accuracy_score(y_test, y_pred),
            'precision_macro': precision_score(y_test, y_pred, average='macro', zero_division=0),
            'recall_macro': recall_score(y_test, y_pred, average='macro', zero_division=0),
            'f1_macro': f1_score(y_test, y_pred, average='macro', zero_division=0),
            'matthews_corrcoef': matthews_corrcoef(y_test, y_pred)
        }
        
         Binary classification specific metrics
        if len(self.classes_) == 2:
            y_pred_proba = self.predict_proba(X_test, scale=scale)[:, 1]
            metrics['roc_auc'] = roc_auc_score(y_test, y_pred_proba)
            metrics['average_precision'] = average_precision_score(y_test, y_pred_proba)
        
        return metrics

 Usage
clf = ComprehensiveClassifier(model_type='random_forest', class_weight='balanced')
clf.create_model(n_estimators=100, max_depth=10)
clf.fit(X_train, y_train)
metrics = clf.comprehensive_evaluation(X_test, y_test)
print(metrics)
```

 4. Core Classification Algorithms

 4.1. Logistic Regression with Advanced Features
```python
class AdvancedLogisticRegression:
    """Logistic regression with calibration and analysis"""
    
    def __init__(self, multi_class='auto', class_weight='balanced'):
        self.multi_class = multi_class
        self.class_weight = class_weight
        self.model = None
        self.calibrated_model = None
    
    def fit_with_calibration(self, X_train, y_train, cv=5):
        """Fit with probability calibration"""
        self.model = LogisticRegression(
            multi_class=self.multi_class,
            class_weight=self.class_weight,
            max_iter=1000,
            random_state=42
        )
        
        self.model.fit(X_train, y_train)
        
         Calibrate probabilities
        self.calibrated_model = CalibratedClassifierCV(
            self.model, method='sigmoid', cv=cv
        )
        self.calibrated_model.fit(X_train, y_train)
        
        return self
    
    def plot_calibration_curve(self, X_test, y_test):
        """Plot calibration curve"""
         Predict probabilities
        y_prob = self.model.predict_proba(X_test)[:, 1]
        y_prob_calibrated = self.calibrated_model.predict_proba(X_test)[:, 1]
        
         Calculate calibration curves
        fraction_of_positives, mean_predicted_value = calibration_curve(y_test, y_prob, n_bins=10)
        fraction_of_positives_cal, mean_predicted_value_cal = calibration_curve(
            y_test, y_prob_calibrated, n_bins=10
        )
        
        fig, axes = plt.subplots(1, 2, figsize=(15, 5))
        
         Calibration plots
        axes[0].plot(mean_predicted_value, fraction_of_positives, "s-", label="Uncalibrated")
        axes[0].plot(mean_predicted_value_cal, fraction_of_positives_cal, "s-", label="Calibrated")
        axes[0].plot([0, 1], [0, 1], "k:", label="Perfectly calibrated")
        axes[0].set_xlabel("Mean Predicted Probability")
        axes[0].set_ylabel("Fraction of Positives")
        axes[0].set_title("Calibration Curves")
        axes[0].legend()
        axes[0].grid(True, alpha=0.3)
        
         Reliability diagram
        axes[1].hist(y_prob, bins=10, edgecolor='black', alpha=0.7)
        axes[1].set_xlabel("Predicted Probability")
        axes[1].set_ylabel("Count")
        axes[1].set_title("Distribution of Predicted Probabilities")
        
        plt.tight_layout()
        return fig

 Usage
adv_lr = AdvancedLogisticRegression()
adv_lr.fit_with_calibration(X_train, y_train)
fig = adv_lr.plot_calibration_curve(X_test, y_test)
```

 4.2. Decision Trees and Ensembles
```python
class TreeBasedClassification:
    """Tree-based classifiers with interpretation"""
    
    def __init__(self):
        self.models = {}
    
    def fit_all_models(self, X_train, y_train):
        """Fit multiple tree-based models"""
        model_configs = {
            'decision_tree': DecisionTreeClassifier(max_depth=10, random_state=42),
            'random_forest': RandomForestClassifier(n_estimators=100, max_depth=10, random_state=42),
            'gradient_boosting': GradientBoostingClassifier(n_estimators=100, random_state=42),
            'adaboost': AdaBoostClassifier(n_estimators=100, random_state=42)
        }
        
        for name, model in model_configs.items():
            print(f"Training {name}...")
            model.fit(X_train, y_train)
            self.models[name] = model
        
        return self
    
    def get_feature_importance(self, feature_names):
        """Extract feature importance from all tree models"""
        importance_dfs = []
        
        for name, model in self.models.items():
            if hasattr(model, 'feature_importances_'):
                importance_df = pd.DataFrame({
                    'feature': feature_names,
                    'importance': model.feature_importances_,
                    'model': name
                }).sort_values('importance', ascending=False)
                importance_dfs.append(importance_df)
        
        return pd.concat(importance_dfs, ignore_index=True)
    
    def plot_feature_importance(self, feature_names, top_n=10):
        """Visualize feature importance"""
        importance_df = self.get_feature_importance(feature_names)
        
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        axes = axes.flatten()
        
        for idx, (model_name, model) in enumerate(self.models.items()):
            if idx >= 4:
                break
            
            if hasattr(model, 'feature_importances_'):
                importances = pd.Series(
                    model.feature_importances_,
                    index=feature_names
                ).nlargest(top_n)
                
                importances.plot(kind='barh', ax=axes[idx])
                axes[idx].set_title(f'{model_name} - Top {top_n} Features')
                axes[idx].grid(True, alpha=0.3)
        
        plt.tight_layout()
        return fig

 Usage
tree_clf = TreeBasedClassification()
tree_clf.fit_all_models(X_train, y_train)
importance = tree_clf.get_feature_importance(X_train.columns)
fig = tree_clf.plot_feature_importance(X_train.columns, top_n=10)
```

 5. Handling Imbalanced Data

 5.1. Imbalanced Classification Strategies
```python
class ImbalancedClassification:
    """Strategies for handling imbalanced datasets"""
    
    def __init__(self):
        self.resampling_methods = {}
    
    def apply_smote(self, X_train, y_train, sampling_strategy='auto', k_neighbors=5):
        """Apply SMOTE oversampling"""
        smote = SMOTE(sampling_strategy=sampling_strategy, k_neighbors=k_neighbors, random_state=42)
        X_resampled, y_resampled = smote.fit_resample(X_train, y_train)
        
        print(f"Original distribution: {pd.Series(y_train).value_counts().to_dict()}")
        print(f"Resampled distribution: {pd.Series(y_resampled).value_counts().to_dict()}")
        
        return X_resampled, y_resampled
    
    def apply_adasyn(self, X_train, y_train):
        """Apply ADASYN oversampling"""
        adasyn = ADASYN(random_state=42)
        X_resampled, y_resampled = adasyn.fit_resample(X_train, y_train)
        
        return X_resampled, y_resampled
    
    def apply_undersampling(self, X_train, y_train, sampling_strategy='auto'):
        """Apply random undersampling"""
        rus = RandomUnderSampler(sampling_strategy=sampling_strategy, random_state=42)
        X_resampled, y_resampled = rus.fit_resample(X_train, y_train)
        
        return X_resampled, y_resampled
    
    def cost_sensitive_learning(self, model_type, class_weights):
        """Cost-sensitive learning with custom class weights"""
        if model_type == 'logistic':
            model = LogisticRegression(class_weight=class_weights, random_state=42, max_iter=1000)
        elif model_type == 'random_forest':
            model = RandomForestClassifier(class_weight=class_weights, random_state=42)
        elif model_type == 'svm':
            model = SVC(class_weight=class_weights, random_state=42, probability=True)
        
        return model
    
    def threshold_tuning(self, y_true, y_prob, metric='f1'):
        """Find optimal classification threshold"""
        from sklearn.metrics import f1_score, precision_score, recall_score
        
        thresholds = np.arange(0.1, 0.9, 0.05)
        scores = []
        
        for threshold in thresholds:
            y_pred = (y_prob >= threshold).astype(int)
            
            if metric == 'f1':
                score = f1_score(y_true, y_pred, zero_division=0)
            elif metric == 'precision':
                score = precision_score(y_true, y_pred, zero_division=0)
            elif metric == 'recall':
                score = recall_score(y_true, y_pred, zero_division=0)
            
            scores.append(score)
        
        optimal_idx = np.argmax(scores)
        optimal_threshold = thresholds[optimal_idx]
        
        return optimal_threshold, thresholds, scores

 Usage
imbalanced_clf = ImbalancedClassification()
X_resampled, y_resampled = imbalanced_clf.apply_smote(X_train, y_train)
optimal_threshold, thresholds, scores = imbalanced_clf.threshold_tuning(y_test, y_prob)
```

 6. Comprehensive Model Evaluation

 6.1. Advanced Evaluation Framework
```python
class ClassificationEvaluator:
    """Comprehensive classification evaluation"""
    
    def __init__(self, model, X_test, y_test, class_names=None):
        self.model = model
        self.X_test = X_test
        self.y_test = y_test
        self.class_names = class_names or np.unique(y_test)
        self.y_pred = model.predict(X_test)
        self.y_prob = model.predict_proba(X_test) if hasattr(model, 'predict_proba') else None
    
    def plot_confusion_matrix(self, normalize=False):
        """Plot confusion matrix with various options"""
        cm = confusion_matrix(self.y_test, self.y_pred)
        
        if normalize:
            cm = cm.astype('float') / cm.sum(axis=1)[:, np.newaxis]
        
        fig, ax = plt.subplots(figsize=(10, 8))
        sns.heatmap(cm, annot=True, fmt='.2f' if normalize else 'd',
                   cmap='Blues', xticklabels=self.class_names,
                   yticklabels=self.class_names, ax=ax)
        ax.set_xlabel('Predicted')
        ax.set_ylabel('Actual')
        ax.set_title(f'Confusion Matrix{" (Normalized)" if normalize else ""}')
        
        return fig
    
    def plot_roc_curves(self):
        """Plot ROC curves for binary or multi-class"""
        if len(self.class_names) == 2:
             Binary classification
            fpr, tpr, _ = roc_curve(self.y_test, self.y_prob[:, 1])
            roc_auc = roc_auc_score(self.y_test, self.y_prob[:, 1])
            
            fig, ax = plt.subplots(figsize=(8, 6))
            ax.plot(fpr, tpr, label=f'ROC Curve (AUC = {roc_auc:.2f})')
            ax.plot([0, 1], [0, 1], 'k--', label='Random Classifier')
            ax.set_xlabel('False Positive Rate')
            ax.set_ylabel('True Positive Rate')
            ax.set_title('ROC Curve')
            ax.legend()
            ax.grid(True, alpha=0.3)
        else:
             Multi-class ROC
            from sklearn.preprocessing import label_binarize
            y_test_bin = label_binarize(self.y_test, classes=self.class_names)
            
            fig, ax = plt.subplots(figsize=(10, 8))
            
            for i, class_name in enumerate(self.class_names):
                fpr, tpr, _ = roc_curve(y_test_bin[:, i], self.y_prob[:, i])
                roc_auc = roc_auc_score(y_test_bin[:, i], self.y_prob[:, i])
                ax.plot(fpr, tpr, label=f'{class_name} (AUC = {roc_auc:.2f})')
            
            ax.plot([0, 1], [0, 1], 'k--')
            ax.set_xlabel('False Positive Rate')
            ax.set_ylabel('True Positive Rate')
            ax.set_title('Multi-class ROC Curves')
            ax.legend()
            ax.grid(True, alpha=0.3)
        
        return fig
    
    def plot_precision_recall_curves(self):
        """Plot precision-recall curves"""
        if len(self.class_names) == 2:
            precision, recall, _ = precision_recall_curve(self.y_test, self.y_prob[:, 1])
            avg_precision = average_precision_score(self.y_test, self.y_prob[:, 1])
            
            fig, ax = plt.subplots(figsize=(8, 6))
            ax.plot(recall, precision, label=f'PR Curve (AP = {avg_precision:.2f})')
            ax.set_xlabel('Recall')
            ax.set_ylabel('Precision')
            ax.set_title('Precision-Recall Curve')
            ax.legend()
            ax.grid(True, alpha=0.3)
        else:
             Multi-class PR curves
            from sklearn.preprocessing import label_binarize
            y_test_bin = label_binarize(self.y_test, classes=self.class_names)
            
            fig, ax = plt.subplots(figsize=(10, 8))
            
            for i, class_name in enumerate(self.class_names):
                precision, recall, _ = precision_recall_curve(y_test_bin[:, i], self.y_prob[:, i])
                avg_precision = average_precision_score(y_test_bin[:, i], self.y_prob[:, i])
                ax.plot(recall, precision, label=f'{class_name} (AP = {avg_precision:.2f})')
            
            ax.set_xlabel('Recall')
            ax.set_ylabel('Precision')
            ax.set_title('Multi-class Precision-Recall Curves')
            ax.legend()
            ax.grid(True, alpha=0.3)
        
        return fig
    
    def generate_report(self):
        """Generate comprehensive classification report"""
        report = classification_report(
            self.y_test, self.y_pred,
            target_names=self.class_names,
            output_dict=True
        )
        
        return pd.DataFrame(report).transpose()

 Usage
evaluator = ClassificationEvaluator(clf.model, X_test, y_test, class_names=['Class 0', 'Class 1'])
evaluator.plot_confusion_matrix(normalize=True)
evaluator.plot_roc_curves()
evaluator.plot_precision_recall_curves()
report = evaluator.generate_report()
print(report)
```

 7. Model Comparison and Selection

 7.1. Multi-Model Comparison Framework
```python
class ClassificationModelComparison:
    """Compare multiple classification models systematically"""
    
    def __init__(self):
        self.models = {}
        self.results = pd.DataFrame()
    
    def add_model(self, name, model):
        """Add model to comparison"""
        self.models[name] = model
    
    def compare_all(self, X_train, X_test, y_train, y_test, cv=5):
        """Compare all models"""
        from sklearn.model_selection import StratifiedKFold
        
        comparison_results = []
        skf = StratifiedKFold(n_splits=cv, shuffle=True, random_state=42)
        
        for name, model in self.models.items():
             Cross-validation scores
            cv_accuracy = cross_val_score(model, X_train, y_train, cv=skf, scoring='accuracy')
            cv_f1 = cross_val_score(model, X_train, y_train, cv=skf, scoring='f1_weighted')
            
             Test set performance
            model.fit(X_train, y_train)
            y_pred = model.predict(X_test)
            
             Get probabilities if available
            if hasattr(model, 'predict_proba'):
                y_prob = model.predict_proba(X_test)
                if len(np.unique(y_test)) == 2:
                    roc_auc = roc_auc_score(y_test, y_prob[:, 1])
                else:
                    roc_auc = roc_auc_score(y_test, y_prob, multi_class='ovr')
            else:
                roc_auc = None
            
            comparison_results.append({
                'Model': name,
                'CV_Accuracy_Mean': cv_accuracy.mean(),
                'CV_Accuracy_Std': cv_accuracy.std(),
                'CV_F1_Mean': cv_f1.mean(),
                'CV_F1_Std': cv_f1.std(),
                'Test_Accuracy': accuracy_score(y_test, y_pred),
                'Test_Precision': precision_score(y_test, y_pred, average='weighted', zero_division=0),
                'Test_Recall': recall_score(y_test, y_pred, average='weighted', zero_division=0),
                'Test_F1': f1_score(y_test, y_pred, average='weighted', zero_division=0),
                'Test_ROC_AUC': roc_auc if roc_auc else np.nan,
                'Matthews_Corr_Coef': matthews_corrcoef(y_test, y_pred)
            })
        
        self.results = pd.DataFrame(comparison_results)
        return self.results.sort_values('Test_F1', ascending=False)

 Usage
model_comparison = ClassificationModelComparison()
model_comparison.add_model('Logistic Regression', LogisticRegression(random_state=42, max_iter=1000))
model_comparison.add_model('Random Forest', RandomForestClassifier(random_state=42))
model_comparison.add_model('Gradient Boosting', GradientBoostingClassifier(random_state=42))
model_comparison.add_model('SVM', SVC(random_state=42, probability=True))

results = model_comparison.compare_all(X_train, X_test, y_train, y_test)
print(results)
```

 8. Best Practices Summary

 8.1. Classification Guidelines
```
✅ DO:
- Always check for class imbalance before training
- Use stratified splits for imbalanced datasets
- Calibrate probabilities for reliable confidence estimates
- Perform cross-validation with stratification
- Consider business costs when choosing metrics (precision vs recall)
- Use ensemble methods for better performance
- Validate model assumptions and check for overfitting

❌ DON'T:
- Use accuracy as the sole metric for imbalanced data
- Ignore false positive/negative costs
- Skip hyperparameter tuning
- Forget to scale features for distance-based algorithms
- Use test set for model selection
- Ignore model interpretability requirements
```

 9. Resources and Further Learning

 9.1. Documentation
- [Scikit-Learn Classification](https://scikit-learn.org/stable/supervised_learning.html)
- [Imbalanced-Learn Documentation](https://imbalanced-learn.org/)
- [Model Evaluation Guide](https://scikit-learn.org/stable/modules/model_evaluation.html)

 9.2. Key Takeaways
- Classification requires careful metric selection based on business needs
- Imbalanced data is common and requires special handling techniques
- Ensemble methods often provide the best performance
- Probability calibration is important for reliable confidence estimates
- Always validate models using multiple metrics and cross-validation

---

Next Steps: Apply these classification techniques to real datasets, practice handling imbalanced data, and build ensemble models for optimal performance.
