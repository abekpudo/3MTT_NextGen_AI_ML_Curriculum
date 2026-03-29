 Machine Learning Algorithms Cheatsheet

 Regression Algorithms

 Linear Regression
```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)
predictions = model.predict(X_test)

 Parameters
coef = model.coef_           Coefficients
intercept = model.intercept_

 Evaluation
from sklearn.metrics import mean_squared_error, r2_score
mse = mean_squared_error(y_test, predictions)
r2 = r2_score(y_test, predictions)
```

 Polynomial Regression
```python
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression

poly = PolynomialFeatures(degree=2)
X_poly = poly.fit_transform(X)

model = LinearRegression()
model.fit(X_poly_train, y_train)
```

 Ridge & Lasso Regression
```python
from sklearn.linear_model import Ridge, Lasso

 Ridge (L2 regularization)
ridge = Ridge(alpha=1.0)

 Lasso (L1 regularization)
lasso = Lasso(alpha=0.1)

 Elastic Net (L1 + L2)
from sklearn.linear_model import ElasticNet
elastic = ElasticNet(alpha=0.5, l1_ratio=0.5)
```

---

 Classification Algorithms

 Logistic Regression
```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
model.fit(X_train, y_train)
predictions = model.predict(X_test)
probabilities = model.predict_proba(X_test)
```

 Decision Trees
```python
from sklearn.tree import DecisionTreeClassifier

model = DecisionTreeClassifier(
    max_depth=5,
    min_samples_split=10,
    min_samples_leaf=5
)
model.fit(X_train, y_train)
```

 Random Forest
```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier(
    n_estimators=100,
    max_depth=10,
    random_state=42
)
model.fit(X_train, y_train)

 Feature importance
importances = model.feature_importances_
```

 Support Vector Machines (SVM)
```python
from sklearn.svm import SVC

model = SVC(kernel='rbf', C=1.0, gamma='scale')
model.fit(X_train, y_train)
```

 K-Nearest Neighbors (KNN)
```python
from sklearn.neighbors import KNeighborsClassifier

model = KNeighborsClassifier(n_neighbors=5)
model.fit(X_train, y_train)
```

 Naive Bayes
```python
from sklearn.naive_bayes import GaussianNB

model = GaussianNB()
model.fit(X_train, y_train)
```

---

 Ensemble Methods

 XGBoost
```python
from xgboost import XGBClassifier

model = XGBClassifier(
    n_estimators=100,
    learning_rate=0.1,
    max_depth=6,
    random_state=42
)
model.fit(X_train, y_train)
```

 Gradient Boosting
```python
from sklearn.ensemble import GradientBoostingClassifier

model = GradientBoostingClassifier(
    n_estimators=100,
    learning_rate=0.1,
    max_depth=5
)
model.fit(X_train, y_train)
```

 AdaBoost
```python
from sklearn.ensemble import AdaBoostClassifier

model = AdaBoostClassifier(
    n_estimators=50,
    learning_rate=1.0
)
model.fit(X_train, y_train)
```

 Voting Classifier
```python
from sklearn.ensemble import VotingClassifier

model = VotingClassifier(
    estimators=[
        ('lr', LogisticRegression()),
        ('rf', RandomForestClassifier()),
        ('svm', SVC())
    ],
    voting='soft'
)
```

---

 Clustering Algorithms

 K-Means
```python
from sklearn.cluster import KMeans

model = KMeans(n_clusters=3, random_state=42)
labels = model.fit_predict(X)

 Inertia (within-cluster sum of squares)
inertia = model.inertia_

 Silhouette score
from sklearn.metrics import silhouette_score
score = silhouette_score(X, labels)
```

 Hierarchical Clustering
```python
from sklearn.cluster import AgglomerativeClustering

model = AgglomerativeClustering(
    n_clusters=3,
    linkage='ward'
)
labels = model.fit_predict(X)
```

 DBSCAN
```python
from sklearn.cluster import DBSCAN

model = DBSCAN(eps=0.5, min_samples=5)
labels = model.fit_predict(X)
```

---

 Model Evaluation

 Classification Metrics
```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score,
    f1_score, confusion_matrix, roc_auc_score,
    classification_report
)

 Binary classification
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)

 Multi-class
report = classification_report(y_test, y_pred)

 ROC-AUC
auc = roc_auc_score(y_test, y_pred_proba)

 Confusion matrix
cm = confusion_matrix(y_test, y_pred)
```

 Regression Metrics
```python
from sklearn.metrics import (
    mean_squared_error, mean_absolute_error,
    r2_score
)

mse = mean_squared_error(y_test, y_pred)
mae = mean_absolute_error(y_test, y_pred)
rmse = np.sqrt(mse)
r2 = r2_score(y_test, y_pred)
```

---

 Hyperparameter Tuning

 Grid Search
```python
from sklearn.model_selection import GridSearchCV

param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [5, 10, 15],
    'learning_rate': [0.01, 0.1, 1.0]
}

grid_search = GridSearchCV(
    XGBClassifier(),
    param_grid,
    cv=5,
    scoring='accuracy'
)
grid_search.fit(X_train, y_train)

print(grid_search.best_params_)
print(grid_search.best_score_)
```

 Random Search
```python
from sklearn.model_selection import RandomizedSearchCV

random_search = RandomizedSearchCV(
    XGBClassifier(),
    param_grid,
    n_iter=20,
    cv=5,
    random_state=42
)
random_search.fit(X_train, y_train)
```

---

 Cross-Validation

```python
from sklearn.model_selection import cross_val_score, KFold

 K-fold cross-validation
scores = cross_val_score(model, X, y, cv=5, scoring='accuracy')
print(f"Scores: {scores}")
print(f"Mean: {scores.mean():.3f} (+/- {scores.std():.3f})")

 Stratified K-fold
from sklearn.model_selection import StratifiedKFold
skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(model, X, y, cv=skf)
```

---

 Pipeline

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', RandomForestClassifier())
])

pipeline.fit(X_train, y_train)
predictions = pipeline.predict(X_test)
```

---

 Model Persistence

```python
import pickle
import joblib

 Save with pickle
with open('model.pkl', 'wb') as f:
    pickle.dump(model, f)

 Load
with open('model.pkl', 'rb') as f:
    model = pickle.load(f)

 Using joblib (better for large models)
joblib.dump(model, 'model.pkl')
model = joblib.load('model.pkl')
```
