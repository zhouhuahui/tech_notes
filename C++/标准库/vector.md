```cpp
vector<cv::Point>::iterator itJoints2 = joints.begin();
	while (itJoints2 != joints.end()) {
		if (imgWithObvMark2.at<uchar>((*itJoints2).y,(*itJoints2).x) != 200)
			itJoints2 = joints.erase(itJoints2); //返回删除位置的后一个元素的迭代器

		else
			itJoints2++;
	}
```

vector中不能放引用。

```cpp
vector<a*&> //写法是错的
```

