# zip

```python
#zip函数传单个参数
list1 = [1,2,3,4]
tuple1 = zip(lis1) #out:zip
list(tuple1) #out:[(1,),(2,),(3,),(4,)]
#zip函数传两个参数
m = [[1,2,3],[4,5,6]]
n = [[1,1],[2,2]]
list(zip(m,n)) #out:[([1, 2, 3], [1, 1]), ([4, 5, 6], [2, 2])]
#*zip()
m = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
n = [[1, 1, 1], [2, 2, 3], [3, 3, 3]]
print(*zip(m,n)) #out:([1, 2, 3], [1, 1, 1]) ([4, 5, 6], [2, 2, 3]) ([7, 8, 9], [3, 3, 3])
m2,n2 = zip(*zip(m,n))
m == list(m2) #out:True
```

# yield

```python
def foo():
    print("starting...")
    while True:
        res = yield 4 #返回值传给调用函数和res
        print("res:",res)
g = foo()
print(next(g))
print("*"*20)
print(next(g))
```

输出是下面的：

```
starting...
4
********************
res: None
4
```

* yield语句类似于return语句。每一次执行next(g)函数，就从上一次执行结束的位置开始执行（若第一次执行next(g)，就从一开始执行），一直执行到下一个含有yield的语句，返回并且释放foo函数所占用的所有内存，就是为什么在第二次执行时会输出```res：None```。

* next(g)语句相当于迭代。

* yield语句常用于生成器对象中，这样可以减少内存开销，在深度学习的数据集生成中很好用。看一下代码

  ```python
  for n in range(1000):
      a=n
  ```

  可以输出0到999的数，但这样的话，range(1000)会默认生成长度为1000的数组，占用内存。

  用生成器更好。

  ```python
  def foo(num):
      print("starting...")
      while num<1000:
          num=num+1
          yield num
  for n in foo(0):
      print(n)
  ```


# list

list是一个可变的有序表，可以向list中追加元素

```python
classmates = list()
classmates.append('zhouhuahui')
```

插入到指定位置

```python
classmates.insert(1,'Jack') #前提是list要至少长度为2
```

删除元素

```python
classmates.pop() #删除末尾元素
classmates.pop(2) #删除索引为2的元素
```

list相加

```python
a = [1,2]
b = [3,4]
print(a+b) #[1,2,3,4]
```

list排序

```python
subset = list()
subset.sort(key=lambda x: micFc[x], reverse=True)#按照micFc[x]的值和降序进行排序
```



# dict

创建一个空的字典

```python
d = {} #以后慢慢赋值
```

字典中是否有某个键，用in

```python
print(5 in d)
```



# string与数字之间的转换

## 数字到字符串

1. 使用格式化字符串的方法

   ```python
   i = 322
   s = '%d' %i
   # %f:浮点数；%e:科学计数
   ```

2. ```str(5)```

## 字符串到数字

1. ```python
   import string
   s = '88'
   i = string.atoi(s)
   f = string.atof(s) #转为浮点数
   ```

2.  ``int('88')``

# python内置函数

## dir()

```python
dir(obj) #查看对象obj的属性和方法
```

## vars()

返回object的属性和属性值的字典对象  

```python
class A:
    a = 1
print(vars(A))
```

## reversed()

返回一个反转的迭代器，操作对象可以是字符串，元组，range，列表

```python
a = 'Run'
print(tuple(reversed(a)))
```



# os模块

### os.path.join()

```python
import os

path1 = 'home'
path2 = 'a.txt'
print(os.path.join(path1,path2)) #home\a.txt
path2 = 'doc'
path3 = 'a.txt'
print(os.path.join(path1,path2,path3)) #home\doc\a.txt
```

### os.listdir()

```python
import os
print(os.listdir("D:\pyproject\day21模块"))
```

# bytes

```python
#bytes转str
s = str(b, encoding='utf-8')
```



# str

## 分割函数

```python
s = 'dlrblist'
a = s.split("l",1) #从左到右找，只分割一次
a = s.rsplit("l",1) #从右向左找，只分割一次
```

## 其他

```python
s.endswith(".jpg") #以.jpg结尾
```

```python
print("abcxyzXy".find("xy")) #3
```

```python
#分割
print('abcxyzxyopq'.partition('xy')) #('abc', 'xy', 'zxyopq')
```



# python调用命令行参数

* ```python
  import os
  
  #Windows返回值为执行命令后shell的返回值,好像没什么用
  os.system("test.exe 1.bmp")
  ```

* ```python
  import os
  
  #该方法通过调用管道的方式来实现的，在调用结束后，会返回一个记录调用输出结果的 file 对象
  pipeline = os.popen("test.exe 1.bmp")
  print(pipeline.read())
  ```

* ```python
  #用于产生子进程，并连接子进程的标准输入输出，功能十分强大，主要用于替代其他几个老的模块和函数
  import subprocess
  
  #shell=True表示子进程的标准输出默认为当前控制台
  p = subprocess.Popen("test.exe",
                       shell=True, 
                      stdout=subprocess.PIPE) 
  p.wait() #阻塞当前线程直到子进程执行结束
  result_lines = p.stdout.readlines() #从子进程的标准输出中读取所有行，并存储在一个list中
  ```

# python数学运算

## decimal包

```python
rom decimal import Decimal
#decimal 可以实现精确的数学运算，所以数字用字符串表示

a = Decimal('4.2')
b = Decimal('2.1')
c = (a+b)/2
print(int(c))
```

# lambda表达式

举个例子

```python
fname_fn = lambda x:'%s.jpg' % x
img_fname = fname_fn("image1")
```

lambda的格式是：``lambda argument_list:expression``    
argument_list是输入，expression是输出

# 读取文件

```python
with open("test.txt","r") as f:
    list1 = f.readlines();
```



