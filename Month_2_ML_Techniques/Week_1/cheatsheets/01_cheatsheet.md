 Week 1 Cheatsheet: Data Preparation & Feature Engineering

 🚀 Quick Reference Guide

---

 📊 EDA (Exploratory Data Analysis)

 Essential Pandas Operations
```python
 Basic Info
df.shape                     (rows, columns)
df.info()                    Data types and non-null counts
df.describe()                Statistical summary
df.dtypes                    Column data types

 Missing Data
df.isnull().sum()            Count missing per column
df.isnull().mean()  100    Percentage missing

 Quick Profile
import pandas_profiling
profile = ProfileReport(df, title="Profiling Report")
profile.to_file("report.html")
```

 Distribution Analysis
```python
 Normality Tests
from scipy import stats

 Shapiro-Wilk (n < 5000)
stat, p = stats.shapiro(df['column'])

 D'Agostino-Pearson (n >= 5000)
stat, p = stats.normaltest(df['column'])

 Kolmogorov-Smirnov
stat, p = stats.kstest(df['column'], 'norm')

 Interpretation
alpha = 0.05
if p > alpha:
    print('Normal distribution')
else:
    print('Not normal distribution')
```

 Correlation Analysis
```python
 Pearson (linear, normal)
corr = df.corr(method='pearson')

 Spearman (monotonic, rank-based)
corr = df.corr(method='spearman')

 Kendall (ordinal, robust)
corr = df.corr(method='kendall')

 Statistical significance
from scipy.stats import pearsonr
corr, p_value = pearsonr(df['x'], df['y'])
```

---

 🧹 Data Cleaning

 Missing Value Strategies
```python
 Detection
missing_report = pd.DataFrame({
    'missing_count': df.isnull().sum(),
    'missing_pct': df.isnull().mean()  100,
    'dtype': df.dtypes
})

 Imputation
from sklearn.impute import SimpleImputer, KNNImputer

 Mean/Median (numerical)
num_imputer = SimpleImputer(strategy='median')

 Most frequent (categorical)
cat_imputer = SimpleImputer(strategy='most_frequent')

 KNN (uses other features)
knn_imputer = KNNImputer(n_neighbors=5)

 Forward fill (time series)
df.fillna(method='ffill', inplace=True)
```

 Outlier Detection
```python
 Statistical Methods
 Z-Score (normal distribution)
from scipy import stats
z_scores = np.abs(stats.zscore(df['column']))
outliers = df[z_scores > 3]

 IQR Method (robust)
Q1 = df['column'].quantile(0.25)
Q3 = df['column'].quantile(0.75)
IQR = Q3 - Q1
outliers = df[(df['column'] < Q1 - 1.5IQR) | (df['column'] > Q3 + 1.5IQR)]

 Isolation Forest (ML-based)
from sklearn.ensemble import IsolationForest
iso_forest = IsolationForest(contamination=0.1, random_state=42)
outliers = iso_forest.fit_predict(df[['column']]) == -1
```

 Data Validation
```python
 Schema Validation
import pandera as pa

schema = pa.DataFrameSchema({
    "age": pa.Column(pa.Int, checks=pa.Check.greater_than(0)),
    "income": pa.Column(pa.Float, checks=pa.Check.greater_than(0)),
    "category": pa.Column(pa.String, checks=pa.Check.isin(['A', 'B', 'C']))
})

validated_df = schema.validate(df)

 Custom Checks
def validate_data(df):
    checks = {
        'no_nulls': df.isnull().sum().sum() == 0,
        'positive_age': (df['age'] > 0).all(),
        'valid_categories': df['category'].isin(['A', 'B', 'C']).all()
    }
    return checks
```

---

 ⚙️ Feature Engineering

 Scaling & Normalization
```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, RobustScaler

 StandardScaler: mean=0, std=1 (normal distributions)
scaler = StandardScaler()
scaled = scaler.fit_transform(df[['feature']])

 MinMaxScaler: range [0,1] (bounded features, neural networks)
scaler = MinMaxScaler()
scaled = scaler.fit_transform(df[['feature']])

 RobustScaler: median/IQR based (outliers present)
scaler = RobustScaler()
scaled = scaler.fit_transform(df[['feature']])
```

 Encoding Categoricals
```python
from sklearn.preprocessing import OneHotEncoder, LabelEncoder, OrdinalEncoder
import category_encoders as ce

 One-Hot Encoding (nominal, low cardinality)
encoder = OneHotEncoder(sparse=False, drop='first')
encoded = encoder.fit_transform(df[['category']])

 Label Encoding (ordinal, tree models)
encoder = LabelEncoder()
encoded = encoder.fit_transform(df['category'])

 Target Encoding (high cardinality, supervised)
encoder = ce.TargetEncoder(cols=['category'])
encoded = encoder.fit_transform(df, df['target'])

 Frequency Encoding
freq_map = df['category'].value_counts().to_dict()
df['category_freq'] = df['category'].map(freq_map)
```

 Numerical Transformations
```python
from sklearn.preprocessing import PowerTransformer
import numpy as np

 Log Transform (right skewed, positive values)
df['log_feature'] = np.log1p(df['feature'])

 Square Root (moderate skew)
df['sqrt_feature'] = np.sqrt(df['feature'])

 Box-Cox (find optimal lambda)
pt = PowerTransformer(method='box-cox')
df['boxcox_feature'] = pt.fit_transform(df[['positive_feature']])

 Yeo-Johnson (handles negative values)
pt = PowerTransformer(method='yeo-johnson')
df['yeo_feature'] = pt.fit_transform(df[['feature']])
```

 Feature Creation
```python
 Interaction Features
df['interaction'] = df['feature1']  df['feature2']

 Polynomial Features
from sklearn.preprocessing import PolynomialFeatures
poly = PolynomialFeatures(degree=2, interaction_only=False)
poly_features = poly.fit_transform(df[['feature1', 'feature2']])

 Binning
df['age_group'] = pd.cut(df['age'], bins=[0, 18, 35, 50, 65, 100], 
                         labels=['0-18', '19-35', '36-50', '51-65', '65+'])

 Date Features
from datetime import datetime
df['year'] = pd.to_datetime(df['date']).dt.year
df['month'] = pd.to_datetime(df['date']).dt.month
df['day_of_week'] = pd.to_datetime(df['date']).dt.dayofweek
df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)
```

 Feature Selection
```python
from sklearn.feature_selection import (
    SelectKBest, mutual_info_classif, 
    RFE, SelectFromModel
)
from sklearn.ensemble import RandomForestClassifier

 Statistical Selection (classification)
selector = SelectKBest(score_func=mutual_info_classif, k=10)
X_selected = selector.fit_transform(X, y)

 Recursive Feature Elimination
estimator = RandomForestClassifier()
selector = RFE(estimator, n_features_to_select=10)
X_selected = selector.fit_transform(X, y)

 Feature Importance
importances = estimator.feature_importances_
feature_importance = pd.DataFrame({
    'feature': X.columns,
    'importance': importances
}).sort_values('importance', ascending=False)
```

---

 🔄 Data Splitting

 Stratified Splitting
```python
from sklearn.model_selection import train_test_split

 Stratified by target
X_train, X_test, y_train, y_test = train_test_split(
    X, y, 
    test_size=0.2, 
    random_state=42,
    stratify=y   Preserve class distribution
)

 Multiple stratification columns
from sklearn.model_selection import StratifiedShuffleSplit

split = StratifiedShuffleSplit(n_splits=1, test_size=0.2)
for train_idx, test_idx in split.split(X, df[['target', 'category']].apply(tuple, axis=1)):
    X_train, X_test = X.iloc[train_idx], X.iloc[test_idx]
```

 Time-Series Splitting
```python
from sklearn.model_selection import TimeSeriesSplit

tscv = TimeSeriesSplit(n_splits=5)
for train_idx, test_idx in tscv.split(X):
    X_train, X_test = X.iloc[train_idx], X.iloc[test_idx]
     Train and evaluate

 Custom time-based split
def time_based_split(df, date_col, train_frac=0.8):
    df = df.sort_values(date_col)
    train_size = int(len(df)  train_frac)
    train = df.iloc[:train_size]
    test = df.iloc[train_size:]
    return train, test
```

 Cross-Validation
```python
from sklearn.model_selection import (
    cross_val_score, StratifiedKFold, 
    GroupKFold, cross_validate
)

 Stratified K-Fold (classification)
cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(model, X, y, cv=cv, scoring='f1_macro')

 Group K-Fold (prevent group leakage)
cv = GroupKFold(n_splits=5)
scores = cross_val_score(model, X, y, groups=groups, cv=cv)

 Multiple metrics
scoring = ['accuracy', 'precision', 'recall', 'f1']
scores = cross_validate(model, X, y, cv=cv, scoring=scoring)
```

---

 🔧 Pipelines

 Complete Preprocessing Pipeline
```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer

 Numerical pipeline
num_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

 Categorical pipeline
cat_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('encoder', OneHotEncoder(handle_unknown='ignore'))
])

 Combine
preprocessor = ColumnTransformer([
    ('num', num_pipeline, numerical_features),
    ('cat', cat_pipeline, categorical_features)
])

 Full pipeline
pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier())
])

 Use
pipeline.fit(X_train, y_train)
predictions = pipeline.predict(X_test)
```

---

 💡 Pro Tips

 Data Leakage Prevention
```python
 ❌ WRONG: Fit on all data
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)   Leakage!
X_train, X_test = train_test_split(X_scaled)

 ✅ CORRECT: Fit only on training data
X_train, X_test = train_test_split(X)
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)   Only transform!
```

 Memory Efficiency
```python
 Downcast types to save memory
def optimize_memory(df):
    for col in df.select_dtypes(include=['int']).columns:
        df[col] = pd.to_numeric(df[col], downcast='integer')
    for col in df.select_dtypes(include=['float']).columns:
        df[col] = pd.to_numeric(df[col], downcast='float')
    return df
```

 Reproducibility Checklist
- [ ] Set random seeds (`random_state=42`)
- [ ] Document all transformations
- [ ] Save preprocessing objects (scalers, encoders)
- [ ] Version control your data
- [ ] Use pipelines to prevent leakage

---

 📚 Essential Commands

| Task | Command/Pattern |
|------|-----------------|
| Quick profile | `df.info()` + `df.describe()` |
| Check nulls | `df.isnull().sum()` |
| Drop duplicates | `df.drop_duplicates()` |
| Fill missing | `df.fillna(strategy)` |
| Encode categories | `OneHotEncoder()` or `pd.get_dummies()` |
| Scale features | `StandardScaler()` or `MinMaxScaler()` |
| Create pipeline | `Pipeline([...])` |
| Stratified split | `train_test_split(..., stratify=y)` |
| Cross-validation | `cross_val_score(model, X, y, cv=5)` |

---

 ⚠️ Common Pitfalls

| ❌ Don't | ✅ Do Instead |
|----------|---------------|
| Fit transformers on all data | Fit on train, transform both |
| Use accuracy with imbalance | Use F1, ROC-AUC, or balanced accuracy |
| Ignore missing patterns | Analyze missingness (MCAR, MAR, MNAR) |
| One-hot high cardinality | Use target or frequency encoding |
| Scale before splitting | Split first, then scale |
| Ignore data types | Explicitly handle categorical vs numerical |

---

Next Week: [Week 2: Supervised Learning →](../Week_2/README.md)
