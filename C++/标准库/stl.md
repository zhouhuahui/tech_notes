# algorithm

**find函数**

```cpp
//find string
#include<algorithm>
string str;
find(str.begin(), str.end(), 'f'); //返回string::iterator类型
```

**sort**

使用algorithm中的sort方法可以高效排序，默认升序。

```cpp
#include<algorithm>
using namespace std;
int main(){
    int a[10] = { 9, 0, 1, 2, 3, 7, 4, 5, 100, 10 };
    sort(a, a +10);
    for (int i = 0; i < 10; i++)
        cout << a[i] << endl;
    return 0;
}
```

```cpp
//用sort对vector进行排序
#include<algorithm>
vector<int> a;
//插入值
sort(a.begin(), a.end());
```

```cpp
//自定义的结构体排序
#include<algorithm>
//从小到大排序
bool comp(const student &a, const student &b){
    return a.score < b.score;
}
int main(){
	vector<student> vectorStudents;
    //插入值    	
  	sort(vectorStudents.begin(),vectorStudents.end(),comp);
    
}
```

**count**

```cpp
vector<string> vStr;
int nRet = std::count(vStr.begin(), vStr.end(), "xiaochun");
```

**find**

```cpp
string s; //或者 vector<> s
find(s.begin(),s.end(),要查找元素) == s.end();
```

**reverse**

```cpp
string s; //或者 vector<> s
reverse(s.begin(),s.end());
```

# regex

https://blog.csdn.net/weixin_42449444/article/details/89022191
基础知识

**regex_match**

regex_match要求str域reg完全匹配，而不是str中包含reg正则表达式

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    string str="{{ name }}";
    regex reg = regex("(\\{\\{ ([^\\{]+) \\}\\})");
    smatch sm;
    regex_match(str,sm,reg);
    cout<<sm.str(0)<<endl; //out: {{ name }}
    cout<<sm.str(1)<<endl; //out: {{ name }}
    cout<<sm.str(2)<<endl; //out: name
}
```

**regex_search**

```cpp
#include <regex>
#include <iostream>
#include <string>

int main() {
    std::string target = "@@abc def--";
    std::regex e("@(\\w+)\\W+(\\w+)");
    std::smatch sm; 
    std::regex_search(target, sm, e); 

    std::cout << "sm.prefix: " << sm.prefix() << std::endl;
    for (int i = 0; i < sm.size(); ++i) {
        std::cout << "sm[" << i << "]: " << sm[i] << std::endl;
    }   
    std::cout << "sm.prefix: " << sm.suffix() << std::endl;

    return 0;
}
```

sm[0]表示完整匹配，也就是``@abc def``, sm[1]到sm[sm.size()]表示各个sub_match，也就是正则表达式中``()``中的匹配内容, ``sm[1]=abc`` ``sm[2]=def`` ,abc与def中间的 `` `` (也就是W+, 非字母，数字，下划线, 所匹配的内容)并不在sm中，因为它没有被括号括起来，所以不能认为是sub_match。``sm.prefix()=="@"``, ``sm.suffix()=="--"``. 

由于smatch是特殊的类，有一些常用的函数:
smatch::str(i) 返回字符串

```cpp
//文本数据
    string str="1994 is my birth year";
    //正则表达式
    string regex_str("\\d{4}");
    regex pattern1(regex_str,regex::icase);

    //迭代器声明
    string::const_iterator iter = str.begin();
    string::const_iterator iterEnd= str.end();
    string temp;
    //正则查找
    while (std::regex_search(iter,iterEnd,result,pattern1))
    {
        temp=result[0];
        cout<<temp<<endl;
        iter = result[0].second; //更新搜索起始位置
    }
```

**regex_replace**

```cpp
std::regex reg1("\\d{4}");
string t("1993");
str = regex_replace(str,reg1,t); //trim_left
cout<<str<<endl;
```

在str查找匹配的文本，并用t中的数据替换。经检验，这个函数会遍历整个文本变量，也就是文本变量中所有符合正则表达式的数据都会被替换

# cmath

```cpp
#include<cmath>
using namespace std;
round(); //返回浮点数，四舍五入
```

# sstream

```cpp
stringstream ss;
string temp;
ss.str(temp); //赋值
int a;
ss >> a; 

while(getline(ss,item,'_')){} //指定字符分割字符串
```

