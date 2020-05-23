# codeblocks-to_string was not declared

这个是MinGW的Bug了。你需要替换codeblocks安装包中的MinGW中的相关文件。

http://tehsausage.com/mingw-to-string 从这个下载zip文件。

将include文件夹下的wchar.h和stdio.h拷贝到MinGW的include文件夹中，一般是.\mingw\include，如果你的codeblocks集成了MinGW则首先要从你的codeblocks安装目录中找到MinGW文件夹，拷贝到其下的include文件夹。

将os_defines.h拷贝到MinGW安装目录的如下目录：
.\mingw\lib\gcc\mingw32\4.7.0\include\c++\mingw32\bits
当然如果codeblocks集成MinGW，你要拷贝到对应的MinGW目录下对应的文件夹。

