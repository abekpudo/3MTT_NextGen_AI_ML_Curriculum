 Data Cleaning: Preparing Data for Reliable Modeling

 1. Introduction
Data cleaning is the critical process of identifying and correcting errors, inconsistencies, and inaccuracies in datasets to improve their quality and reliability. This lecture covers comprehensive techniques for handling missing values, duplicates, outliers, and data formatting issues using Python and modern data science tools.

 2. Why Data Cleaning Matters
- Model Performance: Clean data directly improves model accuracy and reliability
- Computational Efficiency: Reduces processing time and memory usage
- Decision Quality: Enables trustworthy business insights and decisions
- Regulatory Compliance: Ensures data meets quality standards for reporting
- Bias Prevention: Identifies and mitigates biases from data collection issues

 3. The Data Cleaning Framework

 3.1. Systematic Cleaning Workflow
```
┌─────────────────────────────────────────────────────────────────┐
│                    Data Cleaning Pipeline                        │
├─────────────────────────────────────────────────────────────────┤
│ 1. Assessment → Identify issues (missing, duplicates, outliers)│
│ 2. Strategy → Define cleaning approach for each issue type       │
│ 3. Execution → Apply cleaning techniques programmatically      │
│ 4. Validation → Verify cleaning results and data integrity       │
│ 5. Documentation → Record all changes and rationale              │
└─────────────────────────────────────────────────────────────────┘
```

 4. Comprehensive Data Assessment

 4.1. Data Quality Audit System
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
import missingno as msno
import warnings
warnings.filterwarnings('ignore')

class DataQualityAuditor:
    """Comprehensive data quality assessment system"""
    
    def __init__(self, df):
        self.df = df.copy()
        self.quality_report = {}
    
    def full_audit(self):
        """Perform complete data quality audit"""
        print("🔍 COMPREHENSIVE DATA QUALITY AUDIT")
        print("="  60)
        
        self.quality_report['completeness'] = self.assess_completeness()
        self.quality_report['uniqueness'] = self.assess_uniqueness()
        self.quality_report['validity'] = self.assess_validity()
        self.quality_report['consistency'] = self.assess_consistency()
        
        return self.quality_report
    
    def assess_completeness(self):
        """Assess data completeness (missing values)"""
        missing = self.df.isnull().sum()
        missing_pct = (missing / len(self.df))  100
        
        completeness_report = pd.DataFrame({
            'Column': missing.index,
            'Missing_Count': missing.values,
            'Missing_Percentage': missing_pct.values,
            'Completeness_Score': (100 - missing_pct.values).round(2)
        })
        
        completeness_report = completeness_report[
            completeness_report['Missing_Count'] > 0
        ].sort_values('Missing_Percentage', ascending=False)
        
        print(f"\n📊 COMPLETENESS ANALYSIS")
        print(f"Total columns with missing values: {len(completeness_report)}")
        print(f"Overall completeness: {(1 - self.df.isnull().sum().sum() / (len(self.df)  len(self.df.columns)))  100:.2f}%")
        
        if not completeness_report.empty:
            print("\nMissing Data Summary:")
            print(completeness_report.to_string(index=False))
        
        return completeness_report
    
    def assess_uniqueness(self):
        """Assess data uniqueness (duplicates)"""
        total_rows = len(self.df)
        duplicate_rows = self.df.duplicated().sum()
        unique_rows = total_rows - duplicate_rows
        
        uniqueness_report = {
            'total_rows': total_rows,
            'duplicate_rows': duplicate_rows,
            'unique_rows': unique_rows,
            'uniqueness_score': (unique_rows / total_rows)  100
        }
        
        print(f"\n🔄 UNIQUENESS ANALYSIS")
        print(f"Total rows: {total_rows}")
        print(f"Duplicate rows: {duplicate_rows} ({duplicate_rows/total_rows100:.2f}%)")
        print(f"Unique rows: {unique_rows}")
        print(f"Uniqueness score: {uniqueness_report['uniqueness_score']:.2f}%")
        
        return uniqueness_report
    
    def assess_validity(self):
        """Assess data validity (out-of-range values, format issues)"""
        validity_issues = []
        
        for col in self.df.select_dtypes(include=[np.number]).columns:
             Check for infinite values
            inf_count = np.isinf(self.df[col]).sum()
            if inf_count > 0:
                validity_issues.append({
                    'Column': col,
                    'Issue': 'Infinite values',
                    'Count': inf_count
                })
            
             Check for negative values in positive-only columns (example)
            if col in ['age', 'salary', 'price']:
                negative_count = (self.df[col] < 0).sum()
                if negative_count > 0:
                    validity_issues.append({
                        'Column': col,
                        'Issue': 'Negative values in positive-only column',
                        'Count': negative_count
                    })
        
        validity_report = pd.DataFrame(validity_issues)
        
        print(f"\n✓ VALIDITY ANALYSIS")
        if not validity_report.empty:
            print("Validity issues found:")
            print(validity_report.to_string(index=False))
        else:
            print("No validity issues detected")
        
        return validity_report
    
    def assess_consistency(self):
        """Assess data consistency (format standardization)"""
        consistency_issues = []
        
        for col in self.df.select_dtypes(include=['object']).columns:
             Check for inconsistent casing
            if self.df[col].dtype == 'object':
                unique_values = self.df[col].dropna().unique()
                lower_values = [str(v).lower() for v in unique_values]
                
                if len(set(lower_values)) < len(unique_values):
                    consistency_issues.append({
                        'Column': col,
                        'Issue': 'Inconsistent casing',
                        'Example': f"{unique_values[:3]}"
                    })
        
        consistency_report = pd.DataFrame(consistency_issues)
        
        print(f"\n📝 CONSISTENCY ANALYSIS")
        if not consistency_report.empty:
            print("Consistency issues found:")
            print(consistency_report.to_string(index=False))
        else:
            print("No major consistency issues detected")
        
        return consistency_report
    
    def visualize_missing_patterns(self):
        """Visualize missing data patterns"""
        fig, axes = plt.subplots(2, 2, figsize=(15, 10))
        
         Missing data matrix
        msno.matrix(self.df, ax=axes[0,0])
        axes[0,0].set_title('Missing Data Pattern Matrix')
        
         Missing data bar chart
        msno.bar(self.df, ax=axes[0,1])
        axes[0,1].set_title('Missing Data by Column')
        
         Missing data heatmap (correlation)
        msno.heatmap(self.df, ax=axes[1,0])
        axes[1,0].set_title('Missing Data Correlation')
        
         Missing data dendrogram
        msno.dendrogram(self.df, ax=axes[1,1])
        axes[1,1].set_title('Missing Data Clustering')
        
        plt.tight_layout()
        return fig

 Usage
auditor = DataQualityAuditor(df)
quality_report = auditor.full_audit()
fig = auditor.visualize_missing_patterns()
```

 5. Missing Data Handling

 5.1. Missing Data Pattern Analysis
```python
class MissingDataHandler:
    """Advanced missing data handling strategies"""
    
    def __init__(self, df):
        self.df = df.copy()
        self.missing_strategy = {}
    
    def analyze_missing_patterns(self):
        """Analyze patterns in missing data"""
         Missing data by row
        missing_by_row = self.df.isnull().sum(axis=1)
        
         Categorize rows by missingness
        pattern_analysis = {
            'complete_cases': (missing_by_row == 0).sum(),
            'some_missing': ((missing_by_row > 0) & (missing_by_row < len(self.df.columns)/2)).sum(),
            'mostly_missing': (missing_by_row >= len(self.df.columns)/2).sum(),
            'completely_missing': (missing_by_row == len(self.df.columns)).sum()
        }
        
        print("📊 Missing Data Pattern Analysis:")
        for pattern, count in pattern_analysis.items():
            print(f"  {pattern}: {count} rows ({count/len(self.df)100:.1f}%)")
        
        return pattern_analysis
    
    def strategic_imputation(self, column, strategy='auto'):
        """Impute missing values with intelligent strategy selection"""
        
        if strategy == 'auto':
             Auto-select strategy based on data characteristics
            data = self.df[column].dropna()
            skewness = data.skew()
            
            if abs(skewness) > 1:
                strategy = 'median'   Skewed data
            else:
                strategy = 'mean'       Normal distribution
        
        if strategy == 'mean':
            fill_value = self.df[column].mean()
        elif strategy == 'median':
            fill_value = self.df[column].median()
        elif strategy == 'mode':
            fill_value = self.df[column].mode().iloc[0]
        elif strategy == 'constant':
            fill_value = 0   Or specify custom value
        
        self.df[column] = self.df[column].fillna(fill_value)
        print(f"✓ Imputed {column} using {strategy} strategy (value: {fill_value:.2f})")
        
        return self.df
    
    def advanced_imputation_knn(self, column, n_neighbors=5):
        """K-Nearest Neighbors imputation for numerical columns"""
        from sklearn.impute import KNNImputer
        from sklearn.preprocessing import StandardScaler
        
         Select numerical columns for KNN
        numerical_cols = self.df.select_dtypes(include=[np.number]).columns
        
         Standardize data
        scaler = StandardScaler()
        scaled_data = scaler.fit_transform(self.df[numerical_cols])
        
         KNN imputation
        imputer = KNNImputer(n_neighbors=n_neighbors)
        imputed_data = imputer.fit_transform(scaled_data)
        
         Inverse transform
        imputed_df = pd.DataFrame(
            scaler.inverse_transform(imputed_data),
            columns=numerical_cols,
            index=self.df.index
        )
        
        self.df[column] = imputed_df[column]
        print(f"✓ Imputed {column} using KNN imputation (k={n_neighbors})")
        
        return self.df
    
    def multiple_imputation(self, columns, n_imputations=5):
        """Multiple imputation using IterativeImputer"""
        from sklearn.experimental import enable_iterative_imputer
        from sklearn.impute import IterativeImputer
        
        numerical_cols = self.df.select_dtypes(include=[np.number]).columns
        data_to_impute = self.df[numerical_cols]
        
        imputer = IterativeImputer(max_iter=n_imputations, random_state=42)
        imputed_data = imputer.fit_transform(data_to_impute)
        
        imputed_df = pd.DataFrame(
            imputed_data,
            columns=numerical_cols,
            index=self.df.index
        )
        
        for col in columns:
            self.df[col] = imputed_df[col]
        
        print(f"✓ Multiple imputation completed for {len(columns)} columns")
        
        return self.df

 Usage
handler = MissingDataHandler(df)
handler.analyze_missing_patterns()
handler.strategic_imputation('age', strategy='auto')
handler.advanced_imputation_knn('income', n_neighbors=5)
```

 6. Duplicate Detection and Removal

 6.1. Comprehensive Duplicate Handling
```python
class DuplicateHandler:
    """Advanced duplicate detection and handling"""
    
    def __init__(self, df):
        self.df = df.copy()
    
    def detect_duplicates(self, subset=None, keep='first'):
        """Detect and analyze duplicates"""
        
         Exact duplicates
        exact_duplicates = self.df.duplicated(subset=subset, keep=keep)
        exact_count = exact_duplicates.sum()
        
        print(f"🔍 DUPLICATE ANALYSIS")
        print(f"Exact duplicates found: {exact_count}")
        
        if exact_count > 0:
            duplicate_examples = self.df[exact_duplicates].head()
            print("\nExample duplicates:")
            print(duplicate_examples)
        
        return exact_duplicates
    
    def fuzzy_duplicate_detection(self, column, threshold=0.8):
        """Detect near-duplicates using string similarity"""
        from difflib import SequenceMatcher
        
        values = self.df[column].dropna().unique()
        fuzzy_duplicates = []
        
        for i, val1 in enumerate(values):
            for val2 in values[i+1:]:
                similarity = SequenceMatcher(None, str(val1), str(val2)).ratio()
                if similarity > threshold:
                    fuzzy_duplicates.append({
                        'value1': val1,
                        'value2': val2,
                        'similarity': similarity
                    })
        
        if fuzzy_duplicates:
            print(f"⚠️  Found {len(fuzzy_duplicates)} potential fuzzy duplicates in '{column}'")
            fuzzy_df = pd.DataFrame(fuzzy_duplicates).sort_values('similarity', ascending=False)
            print(fuzzy_df.head(10).to_string(index=False))
        
        return pd.DataFrame(fuzzy_duplicates)
    
    def smart_duplicate_removal(self, subset=None, keep='first', inplace=False):
        """Remove duplicates with detailed logging"""
        initial_count = len(self.df)
        
        cleaned_df = self.df.drop_duplicates(subset=subset, keep=keep, inplace=inplace)
        
        if not inplace:
            final_count = len(cleaned_df)
            removed_count = initial_count - final_count
            
            print(f"✓ Duplicate removal completed:")
            print(f"  Initial rows: {initial_count}")
            print(f"  Final rows: {final_count}")
            print(f"  Removed: {removed_count} ({removed_count/initial_count100:.2f}%)")
            
            return cleaned_df
        else:
            final_count = len(self.df)
            print(f"✓ Removed {initial_count - final_count} duplicates")

 Usage
dup_handler = DuplicateHandler(df)
dup_handler.detect_duplicates()
fuzzy_dups = dup_handler.fuzzy_duplicate_detection('name', threshold=0.85)
df_cleaned = dup_handler.smart_duplicate_removal(keep='first')
```

 7. Outlier Detection and Treatment

 7.1. Statistical Outlier Detection
```python
class OutlierHandler:
    """Comprehensive outlier detection and treatment"""
    
    def __init__(self, df):
        self.df = df.copy()
    
    def detect_outliers_iqr(self, column, factor=1.5):
        """Detect outliers using Interquartile Range method"""
        Q1 = self.df[column].quantile(0.25)
        Q3 = self.df[column].quantile(0.75)
        IQR = Q3 - Q1
        
        lower_bound = Q1 - factor  IQR
        upper_bound = Q3 + factor  IQR
        
        outliers = self.df[(self.df[column] < lower_bound) | (self.df[column] > upper_bound)]
        
        outlier_report = {
            'column': column,
            'method': 'IQR',
            'lower_bound': lower_bound,
            'upper_bound': upper_bound,
            'outlier_count': len(outliers),
            'outlier_percentage': (len(outliers) / len(self.df))  100,
            'outlier_indices': outliers.index.tolist()
        }
        
        print(f"📊 IQR Outlier Detection for '{column}':")
        print(f"  Bounds: [{lower_bound:.2f}, {upper_bound:.2f}]")
        print(f"  Outliers found: {outlier_report['outlier_count']} ({outlier_report['outlier_percentage']:.2f}%)")
        
        return outlier_report
    
    def detect_outliers_zscore(self, column, threshold=3):
        """Detect outliers using Z-Score method"""
        z_scores = np.abs(stats.zscore(self.df[column].dropna()))
        outlier_indices = self.df[column].dropna().index[z_scores > threshold]
        
        outlier_report = {
            'column': column,
            'method': 'Z-Score',
            'threshold': threshold,
            'outlier_count': len(outlier_indices),
            'outlier_percentage': (len(outlier_indices) / len(self.df))  100,
            'outlier_indices': outlier_indices.tolist()
        }
        
        print(f"📊 Z-Score Outlier Detection for '{column}':")
        print(f"  Threshold: ±{threshold}")
        print(f"  Outliers found: {outlier_report['outlier_count']} ({outlier_report['outlier_percentage']:.2f}%)")
        
        return outlier_report
    
    def detect_outliers_isolation_forest(self, columns, contamination=0.1):
        """Detect outliers using Isolation Forest"""
        from sklearn.ensemble import IsolationForest
        
        data = self.df[columns].dropna()
        
        iso_forest = IsolationForest(contamination=contamination, random_state=42)
        outlier_labels = iso_forest.fit_predict(data)
        
        outliers = data[outlier_labels == -1]
        
        print(f"🌲 Isolation Forest Outlier Detection:")
        print(f"  Contamination: {contamination100}%")
        print(f"  Outliers detected: {len(outliers)} ({len(outliers)/len(data)100:.2f}%)")
        
        return outliers.index.tolist()
    
    def treat_outliers(self, column, method='cap', outlier_indices=None):
        """Apply outlier treatment strategies"""
        
        if outlier_indices is None:
             Auto-detect outliers
            report = self.detect_outliers_iqr(column)
            outlier_indices = report['outlier_indices']
        
        if method == 'remove':
            self.df = self.df.drop(index=outlier_indices)
            print(f"✓ Removed {len(outlier_indices)} outliers from '{column}'")
            
        elif method == 'cap':
             Winsorization (capping)
            Q1 = self.df[column].quantile(0.25)
            Q3 = self.df[column].quantile(0.75)
            IQR = Q3 - Q1
            lower_bound = Q1 - 1.5  IQR
            upper_bound = Q3 + 1.5  IQR
            
            self.df[column] = self.df[column].clip(lower=lower_bound, upper=upper_bound)
            print(f"✓ Capped outliers in '{column}' to bounds [{lower_bound:.2f}, {upper_bound:.2f}]")
            
        elif method == 'transform':
             Log transformation for right-skewed data
            self.df[column] = np.log1p(self.df[column])
            print(f"✓ Applied log transformation to '{column}'")
        
        return self.df

 Usage
outlier_handler = OutlierHandler(df)
iqr_outliers = outlier_handler.detect_outliers_iqr('price')
zscore_outliers = outlier_handler.detect_outliers_zscore('price', threshold=3)
df_cleaned = outlier_handler.treat_outliers('price', method='cap')
```

 8. Data Standardization and Consistency

 8.1. Text Data Cleaning
```python
class TextDataCleaner:
    """Text data standardization and cleaning"""
    
    def __init__(self, df):
        self.df = df.copy()
    
    def standardize_text(self, column, operations=['lower', 'strip', 'normalize']):
        """Apply text standardization operations"""
        
        if 'lower' in operations:
            self.df[column] = self.df[column].str.lower()
        
        if 'strip' in operations:
            self.df[column] = self.df[column].str.strip()
        
        if 'normalize' in operations:
             Remove extra whitespace
            self.df[column] = self.df[column].str.replace(r'\s+', ' ', regex=True)
        
        print(f"✓ Standardized text in '{column}' with operations: {operations}")
        return self.df
    
    def remove_special_characters(self, column, keep_chars=None):
        """Remove special characters from text"""
        if keep_chars:
             Keep only specified characters and alphanumeric
            pattern = f'[^a-zA-Z0-9\s{re.escape(keep_chars)}]'
        else:
            pattern = r'[^a-zA-Z0-9\s]'
        
        self.df[column] = self.df[column].str.replace(pattern, '', regex=True)
        print(f"✓ Removed special characters from '{column}'")
        return self.df
    
    def fix_common_misspellings(self, column, corrections=None):
        """Fix common misspellings and typos"""
        if corrections is None:
             Example common corrections
            corrections = {
                'nyc': 'new york city',
                'ny': 'new york',
                'calif': 'california',
                'tx': 'texas'
            }
        
        for wrong, correct in corrections.items():
            self.df[column] = self.df[column].str.replace(
                r'\b' + wrong + r'\b', correct, regex=True, case=False
            )
        
        print(f"✓ Applied spelling corrections to '{column}'")
        return self.df

 Usage
text_cleaner = TextDataCleaner(df)
text_cleaner.standardize_text('city', operations=['lower', 'strip', 'normalize'])
text_cleaner.fix_common_misspellings('state')
```

 9. Type Conversion and Validation

 9.1. Smart Type Conversion
```python
class TypeConverter:
    """Intelligent data type conversion"""
    
    def __init__(self, df):
        self.df = df.copy()
    
    def infer_and_convert_types(self):
        """Automatically infer and convert optimal data types"""
        
        print("🔄 TYPE CONVERSION ANALYSIS")
        
        for col in self.df.columns:
            original_type = self.df[col].dtype
            
             Try to convert to numeric
            if self.df[col].dtype == 'object':
                 Check if it can be numeric
                try:
                    numeric_series = pd.to_numeric(self.df[col], errors='coerce')
                    if numeric_series.notna().sum() / len(self.df) > 0.9:   90% success rate
                        self.df[col] = numeric_series
                        print(f"  {col}: {original_type} → {self.df[col].dtype} (numeric)")
                        continue
                except:
                    pass
                
                 Check if it's datetime
                try:
                    datetime_series = pd.to_datetime(self.df[col], errors='coerce')
                    if datetime_series.notna().sum() / len(self.df) > 0.9:
                        self.df[col] = datetime_series
                        print(f"  {col}: {original_type} → datetime")
                        continue
                except:
                    pass
                
                 Check for categorical (low cardinality)
                if self.df[col].nunique() / len(self.df) < 0.05:   Less than 5% unique
                    self.df[col] = self.df[col].astype('category')
                    print(f"  {col}: {original_type} → category")
        
        return self.df
    
    def optimize_memory_usage(self):
        """Optimize memory usage by downcasting numeric types"""
        
        start_mem = self.df.memory_usage(deep=True).sum() / 10242
        
        for col in self.df.select_dtypes(include=[np.number]).columns:
            col_type = self.df[col].dtype
            
            if col_type != object:
                c_min = self.df[col].min()
                c_max = self.df[col].max()
                
                if str(col_type)[:3] == 'int':
                    if c_min > np.iinfo(np.int8).min and c_max < np.iinfo(np.int8).max:
                        self.df[col] = self.df[col].astype(np.int8)
                    elif c_min > np.iinfo(np.int16).min and c_max < np.iinfo(np.int16).max:
                        self.df[col] = self.df[col].astype(np.int16)
                    elif c_min > np.iinfo(np.int32).min and c_max < np.iinfo(np.int32).max:
                        self.df[col] = self.df[col].astype(np.int32)
                else:
                    if c_min > np.finfo(np.float16).min and c_max < np.finfo(np.float16).max:
                        self.df[col] = self.df[col].astype(np.float32)
        
        end_mem = self.df.memory_usage(deep=True).sum() / 10242
        print(f"✓ Memory optimization: {start_mem:.2f} MB → {end_mem:.2f} MB ({100(start_mem-end_mem)/start_mem:.1f}% reduction)")
        
        return self.df

 Usage
converter = TypeConverter(df)
df_converted = converter.infer_and_convert_types()
df_optimized = converter.optimize_memory_usage()
```

 10. Complete Data Cleaning Pipeline

 10.1. Automated Cleaning Pipeline
```python
class DataCleaningPipeline:
    """End-to-end automated data cleaning pipeline"""
    
    def __init__(self, df):
        self.original_df = df.copy()
        self.cleaned_df = df.copy()
        self.cleaning_log = []
    
    def run_full_pipeline(self, config=None):
        """
        Run complete data cleaning pipeline
        
        Config options:
        - handle_missing: True/False
        - remove_duplicates: True/False
        - treat_outliers: True/False
        - standardize_text: True/False
        - optimize_types: True/False
        """
        if config is None:
            config = {
                'handle_missing': True,
                'remove_duplicates': True,
                'treat_outliers': True,
                'standardize_text': True,
                'optimize_types': True
            }
        
        print("🚀 STARTING DATA CLEANING PIPELINE")
        print("="  60)
        print(f"Initial dataset: {self.cleaned_df.shape}")
        
         Step 1: Quality Assessment
        print("\n1️⃣ DATA QUALITY ASSESSMENT")
        auditor = DataQualityAuditor(self.cleaned_df)
        quality_report = auditor.full_audit()
        
         Step 2: Handle Missing Data
        if config['handle_missing']:
            print("\n2️⃣ MISSING DATA HANDLING")
            handler = MissingDataHandler(self.cleaned_df)
            handler.analyze_missing_patterns()
            
             Impute numerical columns
            for col in self.cleaned_df.select_dtypes(include=[np.number]).columns:
                if self.cleaned_df[col].isnull().sum() > 0:
                    handler.strategic_imputation(col, strategy='auto')
        
         Step 3: Remove Duplicates
        if config['remove_duplicates']:
            print("\n3️⃣ DUPLICATE REMOVAL")
            dup_handler = DuplicateHandler(self.cleaned_df)
            self.cleaned_df = dup_handler.smart_duplicate_removal(keep='first')
        
         Step 4: Treat Outliers
        if config['treat_outliers']:
            print("\n4️⃣ OUTLIER TREATMENT")
            outlier_handler = OutlierHandler(self.cleaned_df)
            for col in self.cleaned_df.select_dtypes(include=[np.number]).columns[:3]:
                outlier_handler.treat_outliers(col, method='cap')
        
         Step 5: Standardize Text
        if config['standardize_text']:
            print("\n5️⃣ TEXT STANDARDIZATION")
            text_cleaner = TextDataCleaner(self.cleaned_df)
            for col in self.cleaned_df.select_dtypes(include=['object']).columns:
                if self.cleaned_df[col].str.len().mean() < 100:   Likely categorical
                    text_cleaner.standardize_text(col)
        
         Step 6: Optimize Data Types
        if config['optimize_types']:
            print("\n6️⃣ TYPE OPTIMIZATION")
            converter = TypeConverter(self.cleaned_df)
            self.cleaned_df = converter.infer_and_convert_types()
            self.cleaned_df = converter.optimize_memory_usage()
        
         Final Report
        print("\n📊 CLEANING SUMMARY")
        print("="  60)
        print(f"Original shape: {self.original_df.shape}")
        print(f"Final shape: {self.cleaned_df.shape}")
        print(f"Rows removed: {len(self.original_df) - len(self.cleaned_df)}")
        print(f"Memory change: {self.original_df.memory_usage(deep=True).sum()/10242:.2f} MB → {self.cleaned_df.memory_usage(deep=True).sum()/10242:.2f} MB")
        
        return self.cleaned_df
    
    def save_cleaning_report(self, filename='cleaning_report.txt'):
        """Save detailed cleaning report"""
        with open(filename, 'w') as f:
            f.write("DATA CLEANING REPORT\n")
            f.write("="  60 + "\n\n")
            f.write(f"Original Dataset: {self.original_df.shape}\n")
            f.write(f"Cleaned Dataset: {self.cleaned_df.shape}\n")
            f.write(f"Processing Date: {pd.Timestamp.now()}\n")
        
        print(f"✓ Cleaning report saved to {filename}")

 Usage
pipeline = DataCleaningPipeline(df)
df_cleaned = pipeline.run_full_pipeline()
pipeline.save_cleaning_report()
```

 11. Best Practices Summary

 11.1. Data Cleaning Guidelines
```
✅ DO:
- Always work on a copy of the original data
- Document every cleaning step and rationale
- Validate cleaning results with EDA
- Use domain knowledge to guide decisions
- Test cleaning pipeline on sample data first
- Automate repetitive cleaning tasks
- Preserve data lineage and transformation history

❌ DON'T:
- Delete data without understanding why
- Apply transformations without validation
- Ignore missing data patterns
- Clean training and test data together
- Forget to handle edge cases
- Skip data quality checks after cleaning
```

 12. Resources and Further Learning

 12.1. Documentation
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Scikit-Learn Imputation Guide](https://scikit-learn.org/stable/modules/impute.html)
- [Great Expectations Documentation](https://docs.greatexpectations.io/)

 12.2. Tools and Libraries
- Pandas: Data manipulation and cleaning
- NumPy: Numerical operations
- Scikit-Learn: Advanced imputation methods
- Missingno: Missing data visualization
- Great Expectations: Data validation framework
- FuzzyWuzzy: Fuzzy string matching

 12.3. Best Practices Summary
- Start with comprehensive data quality assessment
- Choose appropriate cleaning strategies for each issue
- Validate all transformations before applying
- Document all cleaning steps and decisions
- Automate repetitive cleaning workflows
- Test pipeline on representative data samples
- Monitor data quality continuously in production

---

Next Steps: Apply these data cleaning techniques to your datasets, build reusable cleaning pipelines, and integrate quality checks into your ML workflows.
