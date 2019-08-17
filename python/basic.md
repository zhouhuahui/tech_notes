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

  

