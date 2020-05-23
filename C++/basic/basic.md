# C语法

1. int main(int argc, char* argv[]);
2. return 0;
3. 不能用for(int i=0;i<n;i++)的形式，只能int i=0; for(i=0;i<n;i++);
4. 没有构造函数
5. const int MAXN=500; int a[MAXN]的写法是错误的，要用: #define MAXN 500
6. 系统自带的scanf不支持long long型

# 强制类型转换

在向上强制转换过程中，使用指针和引用不会造成切割，而使用直接赋值会造成切割。

* static_cast

  ```cpp
  class A 
  {}; 
  class B:public A 
  {}; 
  class C 
  {}; 
  
  int main() 
  { 
      //类间转换
      A* a=new A; 
      B* b; 
      C* c; 
      b=static_cast<B>(a);  // 编译不会报错, B类继承A类 
      c=static_cast<B>(a);  // 编译报错, C类与A类没有任何关系 
      return 1; 
      //基本类型间转换
      int c=static_cast<int>(7.987); 
  }
  ```

* dynamic_cast

  使用dynamic_cast进行转换的，基类中一定要有虚函数，否则编译不通过。需要检测有虚函数的原因：类中存在虚函数，就说明它有想要让基类指针或引用指向派生类对象的情况，此时转换才有意义。 

  在进行下行转换时，dynamic_cast具有类型检查的功能，比 static_cast更安全。向下转换的成功与否还与将要转换的类型有关，即要转换的指针指向的对象的实际类型与转换以后的对象类型一定要相同，否则转换失败。

# 类

```cpp
class A {
public:
	virtual void foo() {
		cout << "A called" << endl;
	}
};
class B: public A {
public:
	void foo() {
		cout << "B1 called" << endl;
	}
	void foo(int a) {
		cout << "B2 called" << endl;
	}
};
int main(int argc, char* argv[])
{
	A* a = new B();
	a->foo(1); //错误的。
}
//很不幸，这种做法是错的，重写的函数（基类中此函数未重载）在子类重载后，基类的指针不能访问到重载的那个函数。
```

### 两个类相互引用

```cpp
//classA.h
typedef struct S1 S1;
typedef struct S2 {
	S1* s1;
	string name;
	S2() {
       //s1 = new S1();//错的；由于S1在S2后面定义，所以不能在构造函数中这样写，只能在类外初始化s1
		name = "s2";
	}
}S2;

struct S1 {
	S2* s2;
	string name;
	S1() {
		name = "s1";
	}
};

class A {
public:
	S1* s1;
	S2* s2;
	void print();
};
//-------------------------------------------------------
```

# 字符串与数字/浮点数的转换

**字符串转数字**

```cpp
#include<cstdio>
char str[] = "1234321";
int a;
double b;
sscanf(str,"%d",&a); //或者sscanf(string.c_str(),"%d",&a);
scanf(str,"%lf",&b);

char str[] = "AF";
int a;
sscanf(str,"%x",&a); //16进制转10进制
```

```cpp
#include<cstring>
stoi(str,0,2); //将str从0开始的2进制数转化为10进制数
```

**数字转字符串**

```cpp
#include<cstdio>
char str[10];
int a = 1234;
sprintf(str,"%d",a);

char str[10];
double a = 123.331;
sprintf(str,"%.3lf",a);

//C++11标准
string to_string(int value);
string to_string(long long value);
string to_string(double value);
```

# c/c++的输入输出流

cin与scanf都都读取到回车，空格，TAB键就结束，回车，空格，TAB不读入

scanf的操作不能有string，因为scanf不支持stl，只能用char* s，或char s[100]

```cpp
char s[100];
scanf("%d",&s);

//scanf读取一行中的所有单词并输出
while(scanf("%s",&s) != EOF){
    printf("%s",s);
}
```

gets(char* buffer) 可以接受一行（忽略换行符)。

**gets的原理与scanf的原理不同，gets读取字符，直到遇到\n; 而scanf先过滤掉非空字符，再读取，直到遇到空字符。gets抛弃\n；scanf接受到非空字符后，遇到空字符，不抛弃**

```cpp
//对于输入
//15\n
//abc de\n
//有两种方法读取
//方法I
scanf("%d\n",&n);
gets(s);
//方法II
scanf("%d",&n);
gets(s); //过滤掉\n
gets(s);
```

**PAT中不能用gets了，只能用fgets读取一行**

```cpp
#include <stdio.h>
char *fgets(char *s, int size, FILE *stream); //size是自定义读入字符的最大个数，包括\0
//如果size=5，而输入是abc\n，则缓冲区中为abc\n\0
//如果size=4，而输入是abc\n，则缓冲区中为abc\0
//如果size=3，而输入为abc\n，则缓冲区为ab\0
```

**getline()**

```cpp
string str;
getline(cin,str); //得到一行
```

读取字符时慎用scanf，用cin

```cpp
char c;
c = cin.get(); //c++写法
//可以读取换行符
c=getc(stdin); //c语言写法
```

```cpp
char s[20];
cin.get(s,5); //除了'\0'，只能读取4个字符
```

**其他应用**

```cpp
//scanf读入16进制数
int a;
scanf("%x",&a);

//sscanf读入16进制数
string str = "a";
int a;
sscanf(str,"%x",&a);
```

```cpp
//输出16进制数
int a=10;
printf("%x",a); //out:a
printf("%X",a); //out:A

char c = '[';
printf("\\x%02X",c); //输出16进制的ASCII码
```

**cout的使用**



---

**常量指针与指针常量**

常量指针：指针指向的值不可变

指针常量：指针不可变

```cpp
typedef char * pchar;
char str1[4]="abc";
char str2[4]="def";
const char *p1 = str1; //p1为常量指针
const pchar p2 = str2; //p2为指针常量
```

**常函数**：C++中不能对成员变量进行修改的成员函数

**new与malloc的区别**

```
0. 属性
new/delete是C++关键字，需要编译器支持;malloc/free是库函数，需要头文件支持
1. 参数
使用new申请内存时无需指定内存块大小，而malloc需要指定内存块大小
2. 返回值
new会返回对象类型的指针，而malloc返回void*，需要用户强制类型转换为对象类型的指针。
3. 分配失败
new分配失败时会抛出异常，而malloc分配失败时会返回NULL
4. new会先申请内存，然后调用对象的构造函数，delete会先调用对象的析构函数，然后再释放内存。
5. new从自由存储区上动态分配内存，malloc从堆上动态分配内存。
```

**C++虚函数机制**

在实例化的对象的头部是指向虚函数表的指针，虚函数表里存放每个虚函数的地址，对于动态类型为子类的实例，其相应的虚函数指针为指向子类虚函数的指针，而不是父类的，所以动态类型为子类和父类的，其虚函数表不同，以此实现多态。

# C语言文件操作

**以字符形式读写文件**

```cpp
File* file=fopen("./test.txt","r");
char c = fgetc(file); //c==EOF表示到达文件尾
```

**文件指针移动**

函数原型

```cpp
int fseek(FILE* stream, long offset, int origin);
```

第一个参数stream为文件指针，复第二个offset为偏移，比如你要从文件的第10000个字节开始读取的话制，offset就应该为10000，origin 为标志是从文件开始还是末尾。
第三个origin 的取值表示移动类型,
SEEK_CUR Current position of [file](https://www.baidu.com/s?wd=file&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) pointer
SEEK_END End of [file](https://www.baidu.com/s?wd=file&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)
SEEK_SET Beginning of file即表示移动类型,分别百代表:当前位置,文件尾,文件头度;
第二个参数正数表示正向偏移，负数表示负向偏移，比如

```cpp
fseek(fp,-1,SEEK_CUR);
```

**写文件**

```cpp
FILE* pFile = fopen("01.txt", "w");
char* str = "C语言";
//fwrite(str, sizeof(char), strlen(str)+1, pFile);
	
//fwrite的返回值，0写入失败 ， 成功返回字符个数*字符大小
int a = fwrite(str, sizeof(char), strlen(str), pFile);
fclose(pFile);//jijiu

//fwrite有个弊端，就是只能写一次，后面的fwrite操作就没用了
```

```cpp
fputs("ss",file); 
//可以累加地写
```

# 枚举类型

```cpp
/**定义枚举类型*/
enum weekday{sun, mon, tue, wed, thu, fri, sat};
```

```cpp
//声明枚举类型变量today
enum weekday today = sun;
```

```cpp
//枚举类型可以用于二维表，如，每个星期吃什么套餐
map<string,weekday> weekdayMap={{"sun",sun}, ...}; //定义string到weekday的映射
int a[7];
a[weekdayMap["sun"]] = 1; //星期日吃1号套餐
```

# 左移

```cpp
1<<5; //2的5次方
```



