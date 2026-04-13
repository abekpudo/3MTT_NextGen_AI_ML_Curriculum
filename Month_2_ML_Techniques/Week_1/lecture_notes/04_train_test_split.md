 Train-Test Split: Evaluating Model Generalization

 1. Introduction
Train-test split is the foundation of reliable machine learning evaluation. It involves partitioning data into separate training and testing sets to assess how well models generalize to unseen data. This lecture covers various splitting strategies, data leakage prevention, and production-ready data partitioning techniques.

 2. Why Train-Test Split Matters
- Generalization Assessment: Measures real-world performance before deployment
- Overfitting Prevention: Ensures models learn patterns, not memorize data
- Model Selection: Provides unbiased comparison between algorithms
- Hyperparameter Tuning: Enables proper validation without test set contamination
- Risk Mitigation: Identifies models that fail on new data before production

 3. Data Splitting Strategies

 3.1. Basic Holdout Method
```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split, StratifiedShuffleSplit
from sklearn.model_selection import TimeSeriesSplit, GroupShuffleSplit
import matplotlib.pyplot as plt
import seaborn as sns

class DataSplitter:
    """Comprehensive data splitting utility"""
    
    def __init__(self, test_size=0.2, random_state=42):
        self.test_size = test_size
        self.random_state = random_state
        self.split_info = {}
    
    def basic_split(self, X, y=None):
        """Basic random train-test split"""
        if y is not None:
            X_train, X_test, y_train, y_test = train_test_split(
                X, y, 
                test_size=self.test_size, 
                random_state=self.random_state
            )
            return X_train, X_test, y_train, y_test
        else:
            train, test = train_test_split(
                X, 
                test_size=self.test_size, 
                random_state=self.random_state
            )
            return train, test
    
    def stratified_split(self, X, y):
        """Stratified split for classification with imbalanced classes"""
        X_train, X_test, y_train, y_test = train_test_split(
            X, y,
            test_size=self.test_size,
            random_state=self.random_state,
            stratify=y
        )
        
         Verify stratification
        train_dist = pd.Series(y_train).value_counts(normalize=True)
        test_dist = pd.Series(y_test).value_counts(normalize=True)
        
        print("Class distribution verification:")
        print(f"Training: {train_dist.to_dict()}")
        print(f"Test: {test_dist.to_dict()}")
        
        return X_train, X_test, y_train, y_test
    
    def time_series_split(self, df, target_col, date_col):
        """Chronological split for time series data"""
        df_sorted = df.sort_values(by=date_col)
        
        split_idx = int(len(df_sorted)  (1 - self.test_size))
        
        train_df = df_sorted.iloc[:split_idx]
        test_df = df_sorted.iloc[split_idx:]
        
        print(f"Time Series Split:")
        print(f"Training: {train_df[date_col].min()} to {train_df[date_col].max()}")
        print(f"Test: {test_df[date_col].min()} to {test_df[date_col].max()}")
        
        return train_df, test_df
    
    def group_split(self, X, y, groups):
        """Group-based split to prevent data leakage from related samples"""
        gss = GroupShuffleSplit(
            n_splits=1, 
            test_size=self.test_size, 
            random_state=self.random_state
        )
        
        for train_idx, test_idx in gss.split(X, y, groups):
            X_train, X_test = X.iloc[train_idx], X.iloc[test_idx]
            y_train, y_test = y.iloc[train_idx], y.iloc[test_idx]
        
        return X_train, X_test, y_train, y_test

 Usage Examples
splitter = DataSplitter(test_size=0.2, random_state=42)

 Basic split
X_train, X_test, y_train, y_test = splitter.basic_split(X, y)

 Stratified split (for classification)
X_train, X_test, y_train, y_test = splitter.stratified_split(X, y)

 Time series split
train_df, test_df = splitter.time_series_split(df, 'target', 'date')
```

 3.2. Advanced Cross-Validation Splitting
```python
from sklearn.model_selection import (
    KFold, StratifiedKFold, TimeSeriesSplit, 
    LeaveOneOut, ShuffleSplit
)

class CrossValidationSplitter:
    """Comprehensive cross-validation strategies"""
    
    def __init__(self, n_splits=5, random_state=42):
        self.n_splits = n_splits
        self.random_state = random_state
    
    def kfold_cv(self, X, y):
        """Standard K-Fold Cross-Validation"""
        kfold = KFold(n_splits=self.n_splits, shuffle=True, random_state=self.random_state)
        
        fold_results = []
        for fold, (train_idx, val_idx) in enumerate(kfold.split(X)):
            fold_results.append({
                'fold': fold,
                'train_size': len(train_idx),
                'val_size': len(val_idx),
                'train_idx': train_idx,
                'val_idx': val_idx
            })
        
        return fold_results
    
    def stratified_kfold_cv(self, X, y):
        """Stratified K-Fold for classification"""
        skf = StratifiedKFold(
            n_splits=self.n_splits, 
            shuffle=True, 
            random_state=self.random_state
        )
        
        fold_results = []
        for fold, (train_idx, val_idx) in enumerate(skf.split(X, y)):
            train_dist = pd.Series(y.iloc[train_idx]).value_counts(normalize=True)
            val_dist = pd.Series(y.iloc[val_idx]).value_counts(normalize=True)
            
            fold_results.append({
                'fold': fold,
                'train_size': len(train_idx),
                'val_size': len(val_idx),
                'train_class_dist': train_dist.to_dict(),
                'val_class_dist': val_dist.to_dict()
            })
        
        return fold_results
    
    def time_series_cv(self, X, n_splits=None):
        """Time Series Cross-Validation"""
        if n_splits is None:
            n_splits = self.n_splits
        
        tscv = TimeSeriesSplit(n_splits=n_splits)
        
        fold_results = []
        for fold, (train_idx, val_idx) in enumerate(tscv.split(X)):
            fold_results.append({
                'fold': fold,
                'train_size': len(train_idx),
                'val_size': len(val_idx),
                'train_start': train_idx[0],
                'train_end': train_idx[-1],
                'val_start': val_idx[0],
                'val_end': val_idx[-1]
            })
        
        return fold_results

 Usage
cv_splitter = CrossValidationSplitter(n_splits=5)

 K-Fold for regression
kfold_results = cv_splitter.kfold_cv(X, y)

 Stratified K-Fold for classification
stratified_results = cv_splitter.stratified_kfold_cv(X, y)

 Time Series CV
ts_results = cv_splitter.time_series_cv(X)
```

 4. Data Leakage Prevention

 4.1. Common Leakage Scenarios and Prevention
```python
class LeakagePrevention:
    """Prevent data leakage in ML pipelines"""
    
    @staticmethod
    def demonstrate_leakage_problem():
        """Show common leakage scenarios"""
        
        print("="  60)
        print("COMMON DATA LEAKAGE SCENARIOS")
        print("="  60)
        
        scenarios = {
            "1. Pre-Split Scaling": """
    ❌ WRONG (Leakage):
        scaler = StandardScaler()
        X_scaled = scaler.fit_transform(X)   Fit on ALL data
        X_train, X_test = train_test_split(X_scaled)
    
    ✅ CORRECT:
        X_train, X_test = train_test_split(X)
        scaler = StandardScaler()
        X_train_scaled = scaler.fit_transform(X_train)
        X_test_scaled = scaler.transform(X_test)
            """,
            
            "2. Target-Dependent Features": """
    ❌ WRONG (Leakage):
        df['feature_mean'] = df.groupby('category')['target'].transform('mean')
        X = df[['feature_mean', 'other_features']]
        X_train, X_test = train_test_split(X)
    
    ✅ CORRECT:
        X_train, X_test, y_train, y_test = train_test_split(X, y)
        train_means = X_train.groupby('category')[y_train.name].mean()
        X_train['feature_mean'] = X_train['category'].map(train_means)
        X_test['feature_mean'] = X_test['category'].map(train_means)
            """,
            
            "3. Future Information in Time Series": """
    ❌ WRONG (Leakage):
        df['future_avg'] = df['value'].rolling(window=10).mean().shift(-5)
        X_train, X_test = train_test_split(df)   Shuffles time!
    
    ✅ CORRECT:
        df_sorted = df.sort_values('timestamp')
        split_idx = int(len(df_sorted)  0.8)
        train_df = df_sorted.iloc[:split_idx]
        test_df = df_sorted.iloc[split_idx:]
            """
        }
        
        for scenario, explanation in scenarios.items():
            print(f"\n{scenario}")
            print(explanation)
    
    @staticmethod
    def validate_no_leakage(X_train, X_test, id_column=None):
        """Validate that no data leakage exists between splits"""
        
        checks = {}
        
         Check for overlapping indices
        if hasattr(X_train, 'index') and hasattr(X_test, 'index'):
            overlap = set(X_train.index) & set(X_test.index)
            checks['index_overlap'] = len(overlap)
            if overlap:
                print(f"⚠️  Warning: {len(overlap)} overlapping indices found!")
        
         Check for ID overlap
        if id_column and id_column in X_train.columns:
            id_overlap = set(X_train[id_column]) & set(X_test[id_column])
            checks['id_overlap'] = len(id_overlap)
            if id_overlap:
                print(f"⚠️  Warning: {len(id_overlap)} overlapping IDs found!")
        
         Check for exact duplicate rows
        if hasattr(X_train, 'values') and hasattr(X_test, 'values'):
            train_tuples = set(map(tuple, X_train.values))
            test_tuples = set(map(tuple, X_test.values))
            duplicate_rows = train_tuples & test_tuples
            checks['duplicate_rows'] = len(duplicate_rows)
        
        return checks

 Demonstrate leakage prevention
LeakagePrevention.demonstrate_leakage_problem()
```

 5. Distribution Validation

 5.1. Train-Test Distribution Comparison
```python
class DistributionValidator:
    """Validate that train and test sets have similar distributions"""
    
    def __init__(self, significance_level=0.05):
        self.alpha = significance_level
    
    def compare_distributions(self, X_train, X_test, y_train=None, y_test=None):
        """Comprehensive distribution comparison"""
        
        results = {}
        
         Numerical features comparison
        numerical_cols = X_train.select_dtypes(include=[np.number]).columns
        for col in numerical_cols:
            train_data = X_train[col].dropna()
            test_data = X_test[col].dropna()
            
             Kolmogorov-Smirnov test
            from scipy.stats import ks_2samp
            ks_stat, ks_pvalue = ks_2samp(train_data, test_data)
            
            results[f'{col}_ks_test'] = {
                'statistic': ks_stat,
                'p_value': ks_pvalue,
                'similar': ks_pvalue > self.alpha
            }
        
         Target distribution comparison (for classification)
        if y_train is not None and y_test is not None:
            train_dist = pd.Series(y_train).value_counts(normalize=True).sort_index()
            test_dist = pd.Series(y_test).value_counts(normalize=True).sort_index()
            
            results['target_distribution'] = {
                'train': train_dist.to_dict(),
                'test': test_dist.to_dict(),
                'max_diff': (train_dist - test_dist).abs().max()
            }
        
        return results
    
    def visualize_distribution_comparison(self, X_train, X_test, columns=None):
        """Visualize train vs test distributions"""
        
        if columns is None:
            columns = X_train.select_dtypes(include=[np.number]).columns[:4]
        
        fig, axes = plt.subplots(2, len(columns), figsize=(5len(columns), 8))
        
        for i, col in enumerate(columns):
             Histograms
            axes[0, i].hist(X_train[col].dropna(), bins=30, alpha=0.7, label='Train', density=True)
            axes[0, i].hist(X_test[col].dropna(), bins=30, alpha=0.7, label='Test', density=True)
            axes[0, i].set_title(f'{col} - Distribution')
            axes[0, i].legend()
            
             Box plots
            data_to_plot = [X_train[col].dropna(), X_test[col].dropna()]
            axes[1, i].boxplot(data_to_plot, labels=['Train', 'Test'])
            axes[1, i].set_title(f'{col} - Box Plot')
        
        plt.tight_layout()
        return fig

 Usage
validator = DistributionValidator()
comparison_results = validator.compare_distributions(X_train, X_test, y_train, y_test)
fig = validator.visualize_distribution_comparison(X_train, X_test)
```

 6. Production-Ready Data Splitting

 6.1. Complete Splitting Pipeline
```python
class ProductionDataSplit:
    """Production-ready data splitting with full validation"""
    
    def __init__(self, 
                 test_size=0.2, 
                 validation_size=0.1,
                 random_state=42,
                 stratify=True):
        self.test_size = test_size
        self.validation_size = validation_size
        self.random_state = random_state
        self.stratify = stratify
        self.split_metadata = {}
    
    def split_data(self, X, y, groups=None):
        """
        Create train/validation/test splits with full validation
        
        Returns:
            dict with X_train, X_val, X_test, y_train, y_val, y_test
        """
        
        print("🚀 Starting Production Data Split Pipeline")
        print("="  60)
        
         Step 1: Initial split (train+val vs test)
        if self.stratify and y is not None:
            stratify_param = y
        else:
            stratify_param = None
        
        if groups is not None:
             Group-based split
            X_temp, X_test, y_temp, y_test = self._group_split(
                X, y, groups, test_size=self.test_size
            )
        else:
            X_temp, X_test, y_temp, y_test = train_test_split(
                X, y,
                test_size=self.test_size,
                random_state=self.random_state,
                stratify=stratify_param
            )
        
         Step 2: Split temp into train and validation
        val_size_adjusted = self.validation_size / (1 - self.test_size)
        
        if self.stratify and y_temp is not None:
            stratify_param = y_temp
        else:
            stratify_param = None
        
        X_train, X_val, y_train, y_val = train_test_split(
            X_temp, y_temp,
            test_size=val_size_adjusted,
            random_state=self.random_state,
            stratify=stratify_param
        )
        
         Step 3: Validation
        self._validate_split(X_train, X_val, X_test, y_train, y_val, y_test)
        
         Store metadata
        self.split_metadata = {
            'train_size': len(X_train),
            'val_size': len(X_val),
            'test_size': len(X_test),
            'train_ratio': len(X_train) / len(X),
            'val_ratio': len(X_val) / len(X),
            'test_ratio': len(X_test) / len(X)
        }
        
        print(f"\n✅ Split completed successfully:")
        print(f"   Train: {len(X_train)} samples ({self.split_metadata['train_ratio']:.1%})")
        print(f"   Validation: {len(X_val)} samples ({self.split_metadata['val_ratio']:.1%})")
        print(f"   Test: {len(X_test)} samples ({self.split_metadata['test_ratio']:.1%})")
        
        return {
            'X_train': X_train, 'X_val': X_val, 'X_test': X_test,
            'y_train': y_train, 'y_val': y_val, 'y_test': y_test
        }
    
    def _group_split(self, X, y, groups, test_size):
        """Group-aware split to prevent leakage"""
        gss = GroupShuffleSplit(n_splits=1, test_size=test_size, random_state=self.random_state)
        
        for train_idx, test_idx in gss.split(X, y, groups):
            X_train, X_test = X.iloc[train_idx], X.iloc[test_idx]
            y_train, y_test = y.iloc[train_idx], y.iloc[test_idx]
        
        return X_train, X_test, y_train, y_test
    
    def _validate_split(self, X_train, X_val, X_test, y_train, y_val, y_test):
        """Validate the split for potential issues"""
        
        print("\n🔍 Validating split integrity...")
        
         Check for overlaps
        validator = LeakagePrevention()
        leakage_checks = validator.validate_no_leakage(X_train, X_test)
        
        if leakage_checks.get('index_overlap', 0) > 0:
            raise ValueError("Data leakage detected: Overlapping indices between train and test!")
        
        print("   ✓ No index overlap detected")
        print("   ✓ No data leakage detected")

 Usage
splitter = ProductionDataSplit(test_size=0.2, validation_size=0.1)
split_data = splitter.split_data(X, y)

X_train = split_data['X_train']
X_val = split_data['X_val']
X_test = split_data['X_test']
```

 7. Best Practices Summary

 7.1. Data Splitting Guidelines
```
✅ DO:
- Always split before any preprocessing
- Use stratification for imbalanced classification
- Set random_state for reproducibility
- Validate distribution similarity after splitting
- Use group-based splits when samples are related
- For time series, always use chronological splits
- Keep test set completely untouched until final evaluation

❌ DON'T:
- Fit preprocessors on the full dataset before splitting
- Shuffle time series data before splitting
- Use test set for hyperparameter tuning
- Ignore data leakage from group structures
- Change random_state between experiments
- Look at test set performance during development
```

 8. Resources and Further Learning

 8.1. Documentation
- [Scikit-Learn Model Selection](https://scikit-learn.org/stable/modules/cross_validation.html)
- [Nested Cross-Validation](https://scikit-learn.org/stable/auto_examples/model_selection/plot_nested_cross_validation_iris.html)
- [Time Series Split](https://scikit-learn.org/stable/modules/cross_validation.htmltime-series-split)

 8.2. Tools and Libraries
- Scikit-Learn: `train_test_split`, `KFold`, `StratifiedKFold`
- Pandas: Data manipulation and indexing
- Scipy: Statistical tests for distribution comparison

---

Next Steps: Apply proper data splitting to your projects, implement leakage checks, and validate your splits before model training.
