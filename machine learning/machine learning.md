# 评价指标

参考https://blog.csdn.net/java1573/article/details/78875098 

```python
sklearn.metrics有很多函数可以计算各种评价指标
```

https://blog.csdn.net/wsljqian/article/details/99435808
python混淆矩阵（confusion_matrix）FP、FN、TP、TN、精确率(Precision),召回率(Recall),准确率(Accuracy)详述.
但是这篇博客，对于confuse_metric的定义过时了，应该是
``第一行: TN,FP; 第二行: FN,TP``

Python sklearn机器学习各种评价指标——Sklearn.metrics简介及应用示例:
https://blog.csdn.net/Yqq19950707/article/details/90169913

# K折交叉验证

```python
from sklearn.model_selection import KFold
kf=KFold(n_splits=num_folds,shuffle=True,random_state=seed)
for train_index,test_index in kf.split(X):
	train_X,train_y=X[train_index],y[train_index]
```

