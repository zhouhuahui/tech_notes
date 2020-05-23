# mutiple definition of 

在编译的时候，出现了mutiple definition of "state"的情况。我的项目是这样的。

```cpp
//main.cpp
#include"lexical_analysis.hpp"
```

```cpp
//lexical_analysis.cpp
#include"lexical_analysis.hpp"
```

```cpp
//lexical_analysis.hpp
#pragma once
int state=0;
```

这样的项目为什么会编译出错呢？因为有两个源文件(main.cpp, lexical_analysis.cpp)包含了lexical_analysis.hpp，所以就有了两次int state=0的定义，这就是错误的地方。

项目修正如下：

```cpp
//main.cpp
#include"lexical_analysis.hpp"
```

```cpp
//lexical_analysis.cpp
int state=0;
#include"lexical_analysis.hpp"
```

```cpp
//lexical_analysis.hpp
#pragma once
extern int state;
```

把全局变量的声明和定义放在.cpp文件中，然后在.hpp文件中extern int state就好了。

