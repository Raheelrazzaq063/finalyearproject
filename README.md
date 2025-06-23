# Project README

This README provides an overview of the Jupyter Notebook titled `viva_code.ipynb`.

## Summary of Notebook Content

### Markdown Cell:
Import Libraries

### Code Cell:
```python
#Import Libraries
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split, GridSearchCV, StratifiedKFold
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
import xgboost as xgb

from imblearn.over_sampling import SMOTE
```

### Markdown Cell:
Load and Clean Datas

### Code Cell:
```python
# Load dataset
df = pd.read_csv("breast-cancer-data.csv")

# Rename typo column
df.rename(columns={'tumer-size': 'tumor-size'}, inplace=True)

# Strip extra apostrophes
df = df.applymap(lambda x: x.strip("'") if isinstance(x, str) else x)

# Convert and fill missing
df['deg-malig'] = pd.to_numeric(df['deg-malig'])
df['node-caps'].fillna(df['node-caps'].mode()[0], inplace=True)
df['breast-quad'].fillna(df['breast-quad'].mode()[0], inplace=True)

# Remove duplicates
df.drop_duplicates(inplace=True)
df.reset_index(drop=True, inplace=True)

```

### Markdown Cell:
Prepare Target and Features

### Code Cell:
```python
# Encode target variable
df['class'] = df['class'].map({'no-recurrence-events': 0, 'recurrence-events': 1})

# One-hot encode categorical features
cat_cols = ['age', 'menopause', 'tumor-size', 'inv-nodes', 'node-caps', 'breast', 'breast-quad', 'irradiate']
df_encoded = pd.get_dummies(df, columns=cat_cols, drop_first=True)

# Define features and target
X = df_encoded.drop('class', axis=1)
y = df_encoded['class']

```

### Markdown Cell:
EDA

### Code Cell:
```python
# -------------------------------------
# Visualizations
# -------------------------------------

# Class distribution
sns.countplot(data=df,
              x='class',
              palette='viridis')      

plt.title('Class Distribution')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

print(df['class'].value_counts())

# Categorical feature distribution by class
sns.set(style="whitegrid", font_scale=1.5)
categorical_cols = ['age', 'menopause', 'tumor-size', 'inv-nodes', 'node-caps', 'breast', 'breast-quad', 'irradiate']
for col in categorical_cols:
    plt.figure(figsize=(18, 8))
    sns.countplot(data=df, x=col, hue='class')
    plt.title(f'{col} vs Class', fontsize=22)
    plt.xlabel(col, fontsize=18)
    plt.ylabel('Count', fontsize=18)
    plt.xticks(rotation=45, ha='right')
    plt.legend(title='Class', fontsize=14)
    plt.tight_layout()
    plt.show()

# Correlation with class
plt.figure(figsize=(12, 10))
corr_matrix = df_encoded.corr()
corr_target = corr_matrix['class'].sort_values(ascending=False)
sns.barplot(x=corr_target.values[1:15], y=corr_target.index[1:15])
plt.title('Top Feature Correlations with Class')
plt.xlabel('Correlation with Recurrence')
plt.ylabel('Features')
plt.tight_layout()
plt.show()
```

### Code Cell:
```python
#Heatmap of Feature Correlations 
plt.figure(figsize=(24, 10))
sns.heatmap(df_encoded.corr(), annot=True, cmap='coolwarm', fmt='.2f')
plt.title('Correlation Heatmap')
plt.tight_layout()
plt.show()

```

### Code Cell:
```python
#Class Distribution per Quadrant and Side
fig, axes = plt.subplots(1, 2, figsize=(16, 6))
sns.countplot(data=df, x='breast-quad', hue='class', ax=axes[0])
axes[0].set_title("Class by Breast Quadrant")
axes[0].tick_params(axis='x', rotation=45)

sns.countplot(data=df, x='breast', hue='class', ax=axes[1])
axes[1].set_title("Class by Breast Side")
axes[1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.show()

```


---

*Note: This is an auto-generated summary containing the first few cells of the notebook. Review the full notebook for comprehensive details.*