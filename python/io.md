# python文件读取

## 读取整个文件

```python
import os
with open('data.txt') as fileIn:
    contents = fileIn.read()
    print(contents.rstrip()) #去除每行文本后面多余的换行，还原为真实的文本
```

若读取的文本中有中文，必须在open()函数中指定```encoding='utf-8'```

## 逐行读取

```python
with open('data.txt') as fileIn:
    line = fileIn.readline()
    print(line)
```

```python
with open('data.txt') as fileIn:
    lines = fileIn.readlines() #将所有行读取到一个列表中，每个元素（一行）依然有冗余换行
```





