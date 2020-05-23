# codeblocks: undefined reference to "func"

![image-20200503100811781](image/image-20200503100811781.png)

我调用lexical_analysis.hpp下的getNextChar函数，函数定义在lexical_analysis.cpp中，但是出现了错误。

原因是, project/properties/build targets 没有设置。

![image-20200503101016398](image/image-20200503101016398.png)