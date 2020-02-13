```python
import numpy as np

a = [[[0,1],
      [2,3]],
     [[4,5],
      [6,7]]]
a = np.array(a)
print(a)
print(a[...,None])
#目的是增加第4维，而且None表示第4维的长度不确定，要系统自己默认，这里默认为1
```

# numpy属性

```python
import numpy as np
a = np.arange(15).reshape(3,5)
print("a.shape=",a.shape)
print("a.ndim=",a.ndim)
print("a.dtype.name=",a.dtype.name)
```

# numpy数组创建

```python
import numpy as np
a = np.ones((3,4),dtype=np.int16)
b = np.arange(10,30,5)#range of [10,30),step is 5
c = np.linspace(0,2,9)#9 numbers from 0 to 2, so step is (2-0)./(9-1)

def f(x,y):
    return 10*x+y
b = np.fromfunction(f,(5,4),dtype=int)  #根据f函数的规则构造（5，4）的数组
```

# numpy操作

## 数学运算

```python
import numpy as np
a = np.array([[1,1],
              [0,1]])
b = np.array([[2,0],
              [3,4]])
a * b                       #elementwise product
a.dot(b)                    #matrix product

a=np.random.random((2,3))
a.sum()                    #将矩阵中元素全部相加
a.min()
a.max()

# by specifying the axis parameter you can apply an operation along the specified axis of an array:
b = np.arange(12).reshape(3,4)
b.sum(axis=0)             #在第0个维度（行）进行相加

b = np.arange(3)
np.exp(b)                 #e的b次方
np.sqrt(b)                
c = np.arange(3)
np.add(b,c)
```

## 切片操作

```python
a = np.arange(10)**3
a[:6:2] = -1000           #equivalent to a[0:6:2],from 0 to 6,but 6 not included,2 is step
a[::-1]                   #reversed a

dots(...)represent as many colons as needed to produce a complete indexing tuple
if x is an array with 5 axes,then
x[4,...,5,:] means x[4,:,:,5,:]

for element in b.flat:
    print(element) #flat is an attribute which is an iterator over all the elements
    
c = a.view()                  #view: c是a的拷贝，但可以通过改变c改变a,这是numpy的独特类型view
s = a[:,1:3]                  #切片返回view类型
d = a.copy()                  #这才是深拷贝
```

## shape manipulation

```python
a = np.random.random((3,4))
a = np.floor(10*a)           #取地板
a.ravel()                    #返回数组，a展平之后的
a.reshape(6,2)               #返回数组，原数组展平后，再按（6，2）分配
a.T                          #返回转置
a.resize((2,6))              #改变a的形状为（2，6）,而且传参和reshape不一样
a.reshape(3,-1)              #-1表示未知长度的维度可根据3来计算，a是（2，6），所以这里是（3，4）

np.vstack((a,b))             #在垂直方向上，也就是第0维度（行）进行堆叠，不改变原来的列维度的大小
np.hstack((a,b))             #与np.vstack相反

np.hsplit(a,3)                #将a切为3块
np.hsplit(a,(3,5))            #将第一个维度（列）的第3行和第4行作为分界线，切割
```

## 索引方式

```python
a = np.arange(12)**2
i = np.array([[3,4],[9,7]])
a[i]            #3替换为a[3],4替换为a[4]

#when the indexed array a is multidimensional,a single array of indices refers to
#the first dimension of a.
palette = np.array([[0,0,0],         #black
                   [255,0,0],        #red
                   [0,255,0],        #green
                   [0,0,255],        #blue
                   [255,255,255]])   #white
image = np.array([[0,1,2,0],         #0代表palette[0],是[0,0,0]
                 [0,3,4,0]])
palette[image]                       #是（2，4，3）shape

#give indexes for more than one dimension
a = np.arange(12).reshape((3,4))
i = np.array([[0,1],
             [1,2]])
j = np.array([[2,1],
             [3,3]])
a[i,j]
a[:,j]                             #内容为[a[0,j],a[1,j],a[2,j]]

data = np.sin(np.arange(20)).reshape(5,4)
data.argmax(axis=0)                #在第0维度的元素之间进行比较，返回最大值所在下标,在这里结果是长度为4的一维向量，每个元素最大为data.shape(0)-1     
data.max(axis=0)                   #在第0维度的元素之间进行比较，返回最大值

#可以给索引的对象赋值
a = np.arange(5)
a[[0,0,2]] = [1,2,3]
a[[0,0,2]]+=1                     #只对a[0]元素操作一次

#用布尔数组进行索引
a = np.arange(12).reshape((3,4))
b = a>4
a[b]                               #返回布尔数组b中为true的那一项，a和b形状一样
```

```python
import numpy as np
a = np.array([2,4,6,8])
print(np.where(a>5))  #结果是(n)的向量，n表示元素值大于5的数量，结果向量的元素是a的下标
a = np.arange(27).reshape((3,3,3))
print(np.where(a>5))  #结果是(3,n)，第一维度的3个元素分别表示x,y,z
```

### np.where

```python
a = np.arange(27).reshape(3,3,3)
np.where(a > 5)
#out
(array([0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2]),
 array([2, 2, 2, 0, 0, 0, 1, 1, 1, 2, 2, 2, 0, 0, 0, 1, 1, 1, 2, 2, 2]),
 array([0, 1, 2, 0, 1, 2, 0, 1, 2, 0, 1, 2, 0, 1, 2, 0, 1, 2, 0, 1, 2]))     
#因为有21个大于5的，所以从左到右有21个，从上到下有3个，表示一维，二维，三维索引
```



## io操作

```python
np.save("a",img)       #保存Numpy数组到文件中，后缀自动为.npy
img = np.load("a.npy")       #加载npy文件
```

