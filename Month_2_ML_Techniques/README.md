 Month 2: Basic & Advanced Machine Learning Techniques

Duration: 4 Weeks | Theme: Core ML Algorithms & Optimization | Credit Hours: Core Concepts

---

 🎯 Month Objectives

By the end of this month, students will:
- ✅ Master supervised learning algorithms
- ✅ Apply unsupervised learning techniques
- ✅ Understand feature engineering and selection
- ✅ Perform hyperparameter tuning and cross-validation
- ✅ Build and evaluate ML pipelines
- ✅ Complete end-to-end ML project

---

 📚 Weekly Breakdown

 Week 1: Data Preparation & Feature Engineering 🔧

Learning Objectives:
- Understand feature engineering importance
- Master data cleaning techniques
- Perform feature selection
- Understand scaling and normalization
- Build data preprocessing pipelines

Topics:

1. Exploratory Data Analysis (EDA) (1 day)
   - Data loading and inspection
   - Statistical summaries
   - Distribution analysis
   - Correlation and covariance
   - Visualization techniques

2. Data Cleaning (1 day)
   - Handling missing values
   - Detecting and treating outliers
   - Data type conversions
   - Duplicate handling
   - Data validation

3. Feature Engineering (1 day)
   - Creating new features
   - Feature transformation
   - Polynomial features
   - Interaction features
   - Domain-specific engineering

4. Feature Scaling & Selection (1 day)
   - Standardization vs Normalization
   - Handling categorical features
   - Feature importance analysis
   - Recursive feature elimination
   - Variance inflation factor

Key Concepts:

| Technique | Purpose | When to Use |
|-----------|---------|------------|
| Mean Imputation | Fill missing values | Simple missing data |
| Forward Fill | Fill time series gaps | Sequential data |
| One-Hot Encoding | Convert categories | Categorical features |
| Label Encoding | Encode ordinal data | Ordered categories |
| StandardScaler | Normalize features | Regression algorithms |
| MinMaxScaler | Scale to range | Neural networks |

Hands-On Practicum:
- [ ] Load and explore dataset (Iris, Boston Housing, or similar)
- [ ] Identify and handle missing values (multiple approaches)
- [ ] Detect and treat outliers using statistical methods
- [ ] Create 5+ new features through engineering
- [ ] Perform feature scaling and normalization
- [ ] Compare model performance with/without scaling
- [ ] Conduct feature importance analysis

Project: Data Preprocessing Pipeline
- [ ] Write functions for each preprocessing step
- [ ] Create reusable preprocessing class
- [ ] Handle edge cases and errors
- [ ] Document and test pipeline
- [ ] Apply to multiple datasets

Deliverables:
- [ ] Preprocessing notebook with full EDA
- [ ] Reusable preprocessing pipeline (Python module)
- [ ] Feature importance visualization
- [ ] Comparison report: model performance before/after preprocessing
- [ ] Documentation of all features and their creation

---

 Week 2: Supervised Learning & Model Evaluation 🎯

Learning Objectives:
- Understand regression algorithms
- Master classification techniques
- Learn proper model evaluation
- Understand bias-variance tradeoff
- Compare algorithms effectively

Topics:

1. Linear & Logistic Regression (1 day)
   - Linear regression theory
   - Normal equation and gradient descent
   - Logistic regression for classification
   - Decision boundaries
   - Interpretation of coefficients

2. Tree-based Models (1 day)
   - Decision tree fundamentals
   - Information gain and Gini impurity
   - Random Forests
   - Gradient Boosting concepts
   - XGBoost and LightGBM

3. Model Evaluation (1 day)
   - Train/test split
   - Regression metrics (MSE, RMSE, R², MAE)
   - Classification metrics (Accuracy, Precision, Recall, F1)
   - ROC curves and AUC
   - Confusion matrices

4. Overfitting & Underfitting (1 day)
   - Understanding bias-variance tradeoff
   - Regularization techniques (L1, L2)
   - Model complexity vs performance
   - Strategies to prevent overfitting

Supervised Learning Algorithms:

| Algorithm | Type | Use Cases | Complexity |
|-----------|------|-----------|-----------|
| Linear Regression | Regression | Continuous predictions | Low |
| Logistic Regression | Classification | Binary classification | Low |
| Decision Trees | Both | Non-linear patterns | Medium |
| Random Forests | Both | Feature importance | Medium |
| SVM | Both | Complex boundaries | High |
| XGBoost | Both | Competitions, real-world | High |

Hands-On Practicum:
- [ ] Implement linear regression from scratch
- [ ] Build logistic regression classifier
- [ ] Create decision tree model
- [ ] Train random forest ensemble
- [ ] Compare algorithm performance
- [ ] Analyze feature importance
- [ ] Visualize decision boundaries
- [ ] Calculate all evaluation metrics

Project: Multi-Algorithm Comparison
- [ ] Implement 5+ algorithms on same dataset
- [ ] Compare performance using multiple metrics
- [ ] Create comparison visualizations
- [ ] Analyze algorithm strengths/weaknesses
- [ ] Make recommendations for production

Deliverables:
- [ ] Comparison notebook with all algorithms
- [ ] Evaluation metrics for each algorithm
- [ ] Performance comparison visualization
- [ ] Feature importance analysis
- [ ] Recommendations and insights document

Common Pitfalls to Avoid:
- ❌ Training on entire dataset (no holdout test)
- ❌ Using accuracy as only metric (imbalanced data)
- ❌ Not checking data leakage
- ❌ Overfitting to validation set
- ❌ Ignoring feature scaling importance

---

 Week 3: Unsupervised Learning & Clustering 🎨

Learning Objectives:
- Master clustering algorithms
- Understand dimensionality reduction
- Learn to work with unlabeled data
- Evaluate clustering quality
- Apply unsupervised techniques

Topics:

1. K-Means Clustering (1 day)
   - Algorithm mechanics
   - Elbow method for choosing K
   - Silhouette score analysis
   - Centroid initialization
   - Scalability considerations

2. Hierarchical Clustering (1 day)
   - Agglomerative clustering
   - Dendrograms and linkage methods
   - Distance metrics
   - Hierarchical vs K-Means comparison
   - Applications and use cases

3. Dimensionality Reduction (1 day)
   - Principal Component Analysis (PCA)
   - t-SNE for visualization
   - Feature extraction vs selection
   - Explained variance
   - Applications in ML pipelines

4. DBSCAN & Other Techniques (1 day)
   - Density-based clustering
   - Advantages over K-Means
   - Gaussian Mixture Models
   - Choosing appropriate algorithms
   - Evaluation metrics (silhouette, Davies-Bouldin)

Unsupervised Learning Algorithms:

| Algorithm | Type | Use Cases | Advantages |
|-----------|------|-----------|-----------|
| K-Means | Clustering | Customer segmentation | Fast, interpretable |
| Hierarchical | Clustering | Taxonomy building | Dendrograms, no K |
| DBSCAN | Clustering | Anomaly detection | Finds arbitrary shapes |
| PCA | Reduction | Visualization, noise | Variance interpretation |
| t-SNE | Reduction | Visualization | Excellent 2D results |

Hands-On Practicum:
- [ ] Implement K-Means from scratch
- [ ] Perform elbow method analysis
- [ ] Calculate and interpret silhouette scores
- [ ] Create dendrograms from hierarchical clustering
- [ ] Apply PCA and visualize results
- [ ] Use t-SNE for 2D/3D visualization
- [ ] Compare clustering algorithms
- [ ] Interpret and explain clusters

Project: Customer Segmentation
- [ ] Load retail or customer dataset
- [ ] Perform extensive EDA
- [ ] Apply multiple clustering algorithms
- [ ] Determine optimal number of clusters
- [ ] Create business insights from clusters
- [ ] Visualize clusters in 2D/3D
- [ ] Recommend customer strategies per segment

Deliverables:
- [ ] Clustering analysis notebook
- [ ] Elbow method and silhouette analysis
- [ ] Cluster visualizations (2D and 3D)
- [ ] Business insights and interpretations
- [ ] Comparison of algorithms
- [ ] Recommendations for business applications

Month 2 Capstone: Airline Passenger Satisfaction Prediction

See [Capstone Guide](./Week_4/capstone_guide.md) for full details.

---

 Week 4: Hyperparameter Tuning & ML Capstone ⚡

Learning Objectives:
- Master hyperparameter optimization
- Understand cross-validation techniques
- Build complete ML pipelines
- Optimize for production
- Complete end-to-end ML project

Topics:

1. Cross-Validation Techniques (1 day)
   - K-Fold cross-validation
   - Stratified K-Fold
   - Time series cross-validation
   - Leave-one-out CV
   - Custom cross-validation splits

2. Hyperparameter Tuning (1 day)
   - Grid Search
   - Random Search
   - Bayesian Optimization
   - Hyperparameter importance
   - Tuning strategies by algorithm

3. Pipeline Creation (1 day)
   - Building ML pipelines
   - Sklearn Pipeline API
   - Feature transformers
   - Avoiding data leakage
   - Pipeline serialization

4. Model Optimization & Deployment (1 day)
   - Model compression
   - Prediction optimization
   - Model serving considerations
   - A/B testing strategies
   - Performance monitoring

Hyperparameter Tuning Workflow:

```
1. Define hyperparameter space
2. Choose search strategy (grid/random/bayesian)
3. Perform cross-validation with each combination
4. Evaluate based on validation metric
5. Train final model with best parameters
6. Evaluate on holdout test set
```

Hands-On Practicum:
- [ ] Implement grid search from scratch
- [ ] Use RandomizedSearch for large spaces
- [ ] Perform stratified K-Fold cross-validation
- [ ] Build sklearn Pipeline
- [ ] Tune hyperparameters systematically
- [ ] Compare tuned vs default parameters
- [ ] Visualize tuning results
- [ ] Save and load models

Project: Airline Passenger Satisfaction

Objective: Build end-to-end ML system to predict passenger satisfaction

Dataset: Airline Passenger Satisfaction Dataset
- Features: Flight distance, cabin class, seat comfort, boarding time, etc.
- Target: Passenger satisfaction (binary or multi-class)
- Samples: ~100K passengers

Requirements:

Phase 1: Exploratory Data Analysis
- [ ] Load and inspect dataset
- [ ] Statistical summaries
- [ ] Distribution visualizations
- [ ] Correlation analysis
- [ ] Missing value analysis
- [ ] Identify patterns and anomalies

Phase 2: Data Preparation
- [ ] Handle missing values
- [ ] Encode categorical features
- [ ] Scale numerical features
- [ ] Handle class imbalance (if applicable)
- [ ] Create new features
- [ ] Split into train/test

Phase 3: Model Development
- [ ] Implement 5+ algorithms
- [ ] Perform hyperparameter tuning
- [ ] Use cross-validation
- [ ] Compare models systematically
- [ ] Select best performing model

Phase 4: Model Evaluation
- [ ] Evaluate on test set
- [ ] Generate confusion matrix
- [ ] Plot ROC curve
- [ ] Calculate all metrics
- [ ] Perform error analysis
- [ ] Create visualization dashboard

Phase 5: Documentation
- [ ] Write comprehensive README
- [ ] Document preprocessing steps
- [ ] Explain model selection
- [ ] Create API documentation
- [ ] Write usage guide

Deliverables:

Required:
- [ ] Jupyter notebook with full analysis
- [ ] Trained model (pickle or joblib)
- [ ] Data preprocessing pipeline
- [ ] Model evaluation report
- [ ] GitHub repository with all code

Enhanced (Extra Credit):
- [ ] Flask/FastAPI endpoint for predictions
- [ ] Docker containerization
- [ ] Interactive visualization dashboard
- [ ] A/B testing framework
- [ ] Model monitoring setup

Evaluation Rubric:

| Component | Points | Criteria |
|-----------|--------|----------|
| EDA & Preprocessing | 20 | Thorough analysis, handling all issues |
| Model Development | 30 | Multiple algorithms, tuning, comparison |
| Evaluation & Results | 25 | Metrics, visualizations, analysis |
| Documentation | 15 | Clear explanations, reproducible code |
| Presentation | 10 | Clear communication of findings |
| Total | 100 | |

---

 🛠️ Tech Stack for Month 2

 Core Libraries
```python
import pandas as pd            Data manipulation
import numpy as np              Numerical computing
from sklearn.model_selection import (
    train_test_split,
    cross_val_score,
    GridSearchCV,
    StratifiedKFold
)
from sklearn.preprocessing import (
    StandardScaler,
    LabelEncoder,
    OneHotEncoder,
    RobustScaler
)
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import (
    classification_report,
    confusion_matrix,
    roc_auc_score,
    roc_curve
)
import xgboost as xgb
import matplotlib.pyplot as plt
import seaborn as sns
```

 Advanced Libraries (Optional)
- `optuna` - Bayesian hyperparameter optimization
- `hyperopt` - Another optimization library
- `shap` - Model explainability
- `eli5` - Model interpretation

---

 📋 Month 2 Checklist

 Week 1
- [ ] Understand EDA and feature engineering importance
- [ ] Clean and preprocess dataset
- [ ] Create new features
- [ ] Perform feature scaling
- [ ] Analyze feature importance

 Week 2
- [ ] Know 8+ supervised learning algorithms
- [ ] Understand evaluation metrics thoroughly
- [ ] Implement algorithms from scratch (linear regression)
- [ ] Compare multiple algorithms
- [ ] Understand bias-variance tradeoff

 Week 3
- [ ] Implement K-Means clustering
- [ ] Perform hierarchical clustering
- [ ] Use PCA for dimensionality reduction
- [ ] Apply t-SNE for visualization
- [ ] Interpret clustering results

 Week 4
- [ ] Perform grid search for hyperparameter tuning
- [ ] Use K-Fold cross-validation
- [ ] Build sklearn Pipeline
- [ ] Complete airline satisfaction project
- [ ] Document everything thoroughly

---

 📖 Recommended Resources

 Books
- "Hands-On Machine Learning" by Aurélien Géron (Chapters 1-10)
- "Introduction to Statistical Learning" by James et al. (ISLR)
- "Machine Learning Yearning" by Andrew Ng

 Online Courses
- scikit-learn official tutorials
- Kaggle Learn: Machine Learning
- Fast.ai: Practical Deep Learning for Coders

 Datasets for Practice
- UCI Machine Learning Repository
- Kaggle Datasets
- Seaborn built-in datasets
- Data.world

 Tools
- Jupyter Notebook
- VS Code with Python extension
- Kaggle Notebooks (cloud-based)
- Google Colab (free GPU)

---

 🚀 Next Month Preparation

Prepare for Month 3 (NLP) by:
- [ ] Learning string operations in Python
- [ ] Understanding basic NLP concepts
- [ ] Installing required NLP libraries
- [ ] Exploring text datasets
- [ ] Reading about tokenization and embeddings

---

Next Month: [Month 3 - NLP →](../Month_3_NLP/README.md)

