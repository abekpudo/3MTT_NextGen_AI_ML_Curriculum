 Python & Jupyter Cheatsheet

 Python Essentials

 Data Types
```python
 String
name = "John"
greeting = f"Hello, {name}"

 Numbers
age = 30                     Integer
height = 5.9                 Float
is_adult = True              Boolean

 Collections
fruits = ["apple", "banana"]   List
colors = ("red", "blue")       Tuple
numbers = {1, 2, 3}            Set
person = {"name": "John", "age": 30}   Dictionary
```

 Control Flow
```python
 if-elif-else
if age < 13:
    print("Child")
elif age < 18:
    print("Teenager")
else:
    print("Adult")

 for loop
for i in range(5):
    print(i)

for fruit in fruits:
    print(fruit)

 while loop
while count < 10:
    count += 1

 List comprehension
squares = [x**2 for x in range(5)]
```

 Functions
```python
def greet(name):
    return f"Hello, {name}"

def add(a, b=0):             Default parameter
    return a + b

def multiple_returns():
    return value1, value2    Return tuple

result = greet("Alice")
```

 Object-Oriented Programming
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def greet(self):
        return f"Hello, I'm {self.name}"
    
    def is_adult(self):
        return self.age >= 18

person = Person("John", 30)
print(person.greet())
```

 NumPy Essentials

```python
import numpy as np

 Create arrays
arr = np.array([1, 2, 3])
zeros = np.zeros(5)          Array of zeros
ones = np.ones((3, 3))       Array of ones
range_arr = np.arange(0, 10, 2)   Like range
linspace = np.linspace(0, 1, 5)   5 evenly spaced

 Reshaping
arr = arr.reshape(2, 2)
arr = arr.flatten()

 Operations
result = arr + 5
result = arr * 2
result = arr ** 2
result = np.sqrt(arr)

 Statistics
mean = np.mean(arr)
std = np.std(arr)
max_val = np.max(arr)
sum_val = np.sum(arr)

 Indexing and slicing
arr[0]                       First element
arr[-1]                      Last element
arr[1:3]                     Elements 1-2
arr[:, 0]                    First column
arr[arr > 5]                 Elements > 5
```

 Pandas Essentials

```python
import pandas as pd

 Create DataFrame
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['NYC', 'LA', 'Chicago']
})

 Load data
df = pd.read_csv('file.csv')
df = pd.read_json('file.json')
df = pd.read_excel('file.xlsx')

 View data
df.head()                    First 5 rows
df.tail()                    Last 5 rows
df.info()                    Data types and info
df.describe()                Summary statistics
df.shape                     Rows, columns

 Select data
df['name']                   Select column
df[['name', 'age']]          Select multiple
df.loc[0]                    Select by label
df.iloc[0]                   Select by position
df[df['age'] > 25]           Filter rows

 Modify data
df['new_col'] = df['age'] * 2
df.drop('city', axis=1)      Drop column
df.dropna()                  Remove null values
df.fillna(0)                 Fill null values

 Aggregations
df.groupby('city').sum()
df.groupby('city').mean()
df.groupby('city').size()

 Sorting
df.sort_values('age')
df.sort_values('age', ascending=False)

 Merging
merged = pd.merge(df1, df2, on='key')
combined = pd.concat([df1, df2])
```

 Scikit-Learn Essentials

```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

 Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

 Scale features
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

 Train model
model = LinearRegression()
model.fit(X_train, y_train)

 Predict
y_pred = model.predict(X_test)

 Evaluate
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
```

 Matplotlib & Seaborn

```python
import matplotlib.pyplot as plt
import seaborn as sns

 Line plot
plt.plot(x, y)
plt.xlabel('X Label')
plt.ylabel('Y Label')
plt.title('Title')
plt.show()

 Scatter plot
plt.scatter(x, y)

 Histogram
plt.hist(data, bins=20)

 Bar plot
plt.bar(categories, values)

 Subplots
fig, axes = plt.subplots(2, 2)
axes[0, 0].plot(x, y)

 Seaborn
sns.heatmap(corr_matrix)
sns.boxplot(data=df, x='category', y='value')
sns.violinplot(data=df, x='x', y='y')
```

 Jupyter Notebook Tips

 Magic Commands
```python
%timeit statement            Time execution
%time statement              Single time
%matplotlib inline           Show plots
%pwd                         Print working directory
%run script.py               Run Python script
!ls                          Run shell command
```

 Debugging
```python
%debug                       Enter debugger
%pdb                         Auto-enter debugger on error
```

 Display
```python
from IPython.display import display, HTML, Image
display(HTML("<h1>Title</h1>"))
display(Image(url='url'))
```

 Common Patterns

 Load and explore data
```python
df = pd.read_csv('data.csv')
print(df.shape)
print(df.info())
print(df.describe())
print(df.head())
```

 Clean data
```python
 Remove nulls
df = df.dropna()

 Handle duplicates
df = df.drop_duplicates()

 Remove outliers
Q1 = df['column'].quantile(0.25)
Q3 = df['column'].quantile(0.75)
IQR = Q3 - Q1
df = df[(df['column'] >= Q1 - 1.5*IQR) & (df['column'] <= Q3 + 1.5*IQR)]
```

 Train and evaluate model
```python
from sklearn.model_selection import cross_val_score

model = RandomForestClassifier()
scores = cross_val_score(model, X, y, cv=5)
print(f"Accuracy: {scores.mean():.3f}")

model.fit(X_train, y_train)
accuracy = model.score(X_test, y_test)
```

 Virtual Environment

```bash
 Create
python -m venv venv

 Activate
source venv/bin/activate   macOS/Linux
venv\Scripts\activate      Windows

 Requirements
pip freeze > requirements.txt
pip install -r requirements.txt
```
