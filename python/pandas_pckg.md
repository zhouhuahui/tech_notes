```python
import pandas as pd
data = pd.read_csv(filename,sep="\s+") #data是表格型数据结构dataframe
data['gene'] #访问属性为gene的那一列的值
data.values #二维数组，表的值
data.columns #返回所有的列属性，如gene,f1,f2,f3
data[columns] #返回列为columns的那一列的所有值
```

