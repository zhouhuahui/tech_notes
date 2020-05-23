# 数据插入

**返回值**

```cpp
pair<set<int>::iterator,bool> demo=set.insert(1);
//first是插入的结果的迭代器，second是表示是否成功
```

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

**如果set中不是int怎么办, 而是自定义的struct**

```cpp
struct Commodity {  //商品类
    long long id, score;  //id和分数
    Commodity(long long i, long long s) : id(i), score(s) {}
    bool operator<(const Commodity& c) const {  //重载小于运算符
        return this->score != c.score ? this->score > c.score : this->id < c.id;
    }
}; //在struct中重载小于运算符
```

# 数据删除

```cpp
set<int> set1, set2;
set1.erase(set1.begin());
set1.erase(set1.begin(), set1.end());
set1.erase(5);
```

