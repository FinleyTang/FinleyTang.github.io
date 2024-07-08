# 使用 `scikit-learn` 进行机器学习模型训练与调参——学习笔记

## 引言
在这篇学习笔记中，我们将使用 `scikit-learn`（也称为 `scikit-learn`）进行机器学习模型的训练与调参。具体示例将采用随机森林分类器和鸢尾花数据集。整个过程将包括数据加载、数据预处理、模型训练、评估及超参数调优。

## 步骤概述

1. **加载数据**：从文件、数据库或其他来源加载数据。
2. **数据预处理**：清洗和准备数据，包括处理缺失值、标准化特征等。
3. **分割数据集**：将数据集分为训练集和测试集。
4. **选择模型**：选择适合任务的机器学习模型。
5. **训练模型**：使用训练数据拟合模型。
6. **评估模型**：在测试数据上评估模型性能。
7. **调参**：使用交叉验证和超参数优化进一步提升模型性能。
8. **预测**：使用训练好的模型对新数据进行预测。

## 代码示例

### 数据加载与预处理

首先，我们加载鸢尾花数据集并进行数据预处理。

```python
import numpy as np
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix

# 1. 加载数据
iris = load_iris()
X = iris.data
y = iris.target

# 打印前5个样本数据和标签
print("原始数据（前5个样本）：")
print(pd.DataFrame(X, columns=iris.feature_names).head())
print("对应的标签：")
print(y[:5])

# 2. 数据预处理
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# 打印前5个样本的标准化后数据
print("\n标准化后的数据（前5个样本）：")
print(pd.DataFrame(X_scaled, columns=iris.feature_names).head())

# 3. 分割数据集
X_train, X_test, y_train, y_test = train_test_split(X_scaled, y, test_size=0.3, random_state=42)

# 打印训练集和测试集的大小
print("\n训练集大小：", X_train.shape)
print("测试集大小：", X_test.shape)
```

### 模型训练与评估

选择随机森林分类器作为模型，并进行训练和评估。

```python
# 4. 选择模型
model = RandomForestClassifier(n_estimators=100, random_state=42)

# 5. 训练模型
model.fit(X_train, y_train)

# 6. 评估模型
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)
report = classification_report(y_test, y_pred)
conf_matrix = confusion_matrix(y_test, y_pred)

print(f"\nAccuracy: {accuracy:.2f}")
print("Classification Report:")
print(report)
print("Confusion Matrix:")
print(conf_matrix)
```

### 超参数调优

使用 `GridSearchCV` 进行超参数调优，以进一步提升模型性能。

#### 超参数调优的概念

超参数调优是指通过系统化的方法来选择模型的超参数，以优化模型的性能。超参数是在训练之前设置的，不是在训练过程中学习的。例如，对于随机森林模型，常见的超参数包括树的数量（`n_estimators`）、树的最大深度（`max_depth`）等。

#### 使用 `GridSearchCV` 进行超参数调优

`GridSearchCV` 是 `scikit-learn` 提供的一种方法，可以自动化地通过交叉验证搜索指定参数空间的最佳参数组合。

```python
from sklearn.model_selection import GridSearchCV

# 定义参数网格
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_features': ['auto', 'sqrt', 'log2'],
    'max_depth': [4, 6, 8, 10],
    'criterion': ['gini', 'entropy']
}

# 设置 GridSearchCV
grid_search = GridSearchCV(RandomForestClassifier(random_state=42), param_grid, cv=5)
grid_search.fit(X_train, y_train)

# 输出最佳参数和交叉验证得分
print(f"\nBest parameters: {grid_search.best_params_}")
print(f"Best cross-validation score: {grid_search.best_score_:.2f}")

# 使用最优参数训练模型
best_model = grid_search.best_estimator_
best_model.fit(X_train, y_train)
best_y_pred = best_model.predict(X_test)

# 评估最优模型
best_accuracy = accuracy_score(y_test, best_y_pred)
best_report = classification_report(y_test, best_y_pred)
best_conf_matrix = confusion_matrix(y_test, best_y_pred)

print(f"\nBest Model Accuracy: {best_accuracy:.2f}")
print("Best Model Classification Report:")
print(best_report)
print("Best Model Confusion Matrix:")
print(best_conf_matrix)
```

### 新数据预测

使用训练好的模型对新数据进行预测。

```python
# 8. 预测
new_data = [[5.1, 3.5, 1.4, 0.2]]  # 这里是新的数据点
new_data_scaled = scaler.transform(new_data)
prediction = best_model.predict(new_data_scaled)
print(f"\nPrediction for new data: {iris.target_names[prediction][0]}")
```

## 总结

通过这篇学习笔记，我们了解了如何使用 `scikit-learn` 进行机器学习模型的训练与评估，并通过 `GridSearchCV` 进行超参数调优。我们选择了随机森林分类器，并在鸢尾花数据集上进行了实际操作，展示了从数据加载、预处理、模型选择与训练、评估到超参数调优的完整过程。希望这篇笔记能帮助你更好地理解和应用 `scikit-learn` 进行机器学习任务。