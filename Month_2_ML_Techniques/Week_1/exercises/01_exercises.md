 Week 2 Exercises: EDA, Cleaning, and Feature Engineering

 Objectives
Master the practical application of data preprocessing and exploratory analysis using real-world datasets.

 1. Exploratory Data Analysis (EDA)
- Load the `Titanic` dataset (or a similar dataset like `Tips` from Seaborn).
- Generate summary statistics for all numerical and categorical columns.
- Create a histogram for `Age` and a count plot for `Survived`.
- Generate a correlation heatmap and identify the two features most correlated with the target.

 2. Data Cleaning
- Identify all columns with missing values.
- Numerical: Fill missing `Age` values with the median.
- Categorical: Fill missing `Embarked` values with the mode.
- Check for and remove any duplicate rows.
- Detect outliers in the `Fare` column using a box plot and decide whether to trim or cap them.

 3. Feature Engineering
- Encoding: Convert the `Sex` column into numerical values (One-Hot or Label Encoding).
- Transformation: Create a new feature `FamilySize` by summing `SibSp` and `Parch`.
- Scaling: Apply `StandardScaler` to the `Fare` and `Age` columns.
- Binning: Create a new categorical feature `AgeGroup` (e.g., Child, Adult, Senior).

 4. Train-Test Split
- Separate your features (`X`) from your target (`y`).
- Perform an 80/20 split using `train_test_split`.
- Challenge: Use the `stratify` parameter to ensure the survival rate is identical in both sets.
- Verify the shapes of `X_train`, `X_test`, `y_train`, and `y_test`.

 5. Validation
- Compare the mean of the `Fare` column before and after scaling.
- Briefly document one interesting insight you found during your EDA (e.g., "Women had a significantly higher survival rate").

---
