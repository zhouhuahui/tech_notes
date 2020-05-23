# pair

## 定义

``pair<T1,T2> p>``

``pair<T1,T2> p(v1,v2)``

``make_pair(v1,v2)``

## 操作

``p.first``

``p.second``

# map

## 插入

```cpp
map<int, int> mp;
mp[0] = 0;

pair<map<int,int>::iterator,bool> res=mp.insert(make_pair(1, 1)); 

map<int,int>::iterator beg, end;
mp.insert(beg,end);
```

## 查找

```cpp
map<int, int>::iterator it_find;
        it_find = mp.find(0);
        if (it_find != mp.end()){
                it_find->second = 20;
        }else{
                printf("no!\n");
        }
```

## 比较

若map的key不是int而是一个类，怎么比较键的大小呢？

```cpp
struct ptr_less : public binary_function<Point, Point, bool>
{
	bool operator()(const Point& a, const Point& b) const
	{
		if (a.x > b.x)
			return true;
		else if (a.y > b.y)
			return true;
		else return false;
	}
};

int main(){
	map<Point, vector<pair<int, int>>, ptr_less> m; //这样就可以从大到小排序了
}
//其实还是有错误
```

```cpp
//上面的代码会产生 invalid comparator的运行时错误
//改正
struct ptr_less : public binary_function<Point, Point, bool>
{
	bool operator()(const Point& a, const Point& b) const
	{
		if (a.x > b.x)
			return true;
		else if (a.x == b.x && a.y > b.y)
			return true;
		else return false;
	}
};

int main(){
	map<Point, vector<pair<int, int>>, ptr_less> m; //这样就可以从大到小排序了
}
//comparator需要是strict weak ordering，需要注意的是，comparator(x,x)必须返回false。
```

## 查找

```cpp
map<int,int> mp;
map<int,int>::iterator it = mp.find(9);
```

