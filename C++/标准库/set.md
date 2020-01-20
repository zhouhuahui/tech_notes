# 数据插入

```cpp
//自动去重，非降排序
set<int> s;
	s.insert(2);
	s.insert(1);
	for (set<int>::iterator it = s.begin(); it != s.end(); it++)
		printf("%d", *it); 
```

