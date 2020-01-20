# python文件读写

## 读取整个文件

```python
import os
with open('data.txt') as fileIn:
    contents = fileIn.read()
    print(contents.rstrip()) #去除每行文本后面多余的'\n' '\t' 'lr'，还原为真实的文本。lstrip()删除开头的，strip删除开头和结尾的
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

## 写文件

```python
file = open('data.txt','w')
```

# 问题

## 如何访问一个文件夹中的内容

```python
import os
files = os.listdir(filepath)
```





