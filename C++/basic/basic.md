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

