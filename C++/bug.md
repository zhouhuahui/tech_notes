```cpp
vector<BigSeg*> vbs;
//vbs初始化
//中间步骤
delete vbs.at(5);

//错误原因，在delete vbs中的一个元素后没有及时把它从vbs中移出，造成vbs[5]成为野指针
```

