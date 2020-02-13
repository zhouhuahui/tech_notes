# 数据插入

```cpp
//自动去重，非降排序
set<int> s;
	s.insert(2);
	s.insert(1);
	for (set<int>::iterator it = s.begin(); it != s.end(); it++)
		printf("%d", *it); 

set<int> A;
s.insert(A.begin(),A.end()); //批量插入，相当于union(s,A)的操作
```

# 数据删除

```cpp
set<int> set1, set2;
set1.erase(set1.begin());
set1.erase(set1.begin(), set1.end());
set1.erase(5);
```

