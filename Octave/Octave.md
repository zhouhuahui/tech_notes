**变量**

deg = pi/180

clear deg 删除变量

```matlab
format long 
deg = pi/180
//显示15位精度
format short
deg 
```

复数
3+4i

无穷大
Inf

非数值
NaN

**再如何保存数据**

save anyname 将所有变量保存到anyname.mat

load anyname

**矩阵**

![1574661623836](D:\GitRep\tech_notes\Octave\image\1574661623836.png)

```matlab
#提取矩阵元素
A(i,j)
```

A' 求转置矩阵

```matlab
a=[1:2:6 -1 0]
a(3)
c=[4;7;10]
a.*b //element-wise multiplication
```

```matlab
plot(x,y)
```

```matlab
rand(m, n) #函数生成一个m行n列的矩阵，矩阵的每个元是0到1之间的一个随机数
```

matlab中r(5,5,5)是按照第三维优先来存储三维数组的

**控制语句**

for n=1:5
end

if expression
	elseif
	else
end

switch x
	case 
	case
	otherwise
end

**画图**

```matlab
subplot(2,2,select);
```

