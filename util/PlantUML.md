# 官网

http://plantuml.com/zh/

[在线作图网址](http://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000)

# 使用方法

在在线作图网址里面有编码区，可以做UML图，做完之后可以submit，然后作品会保存在服务器里面，在这个页面也可以找到保存作品的网址，可以方便地插入到markdown文件中。

# 语法

## 类图

1. 标题

   ```
   Title "类图"
   ```

2. 注释

   所有以单引号开头的行 ’ 都是注释。你也可以使用多行注释,多行注释以 /’ 开头 ‘/ 结尾。

3. 方法和属性地访问权限

   | 标识 |   属性    |
   | :--: | :-------: |
   |  -   |  private  |
   |  #   | protected |
   |  +   |  public   |
   |  ~   |  package  |

4. 类关系

   * 泛化或实现

     ```cpp
     Father <|-- Son
     //若Father是interface,则为实现关系，若为抽象类或普通类，则为泛化关系
     ```

   * 组合

     ```cpp
     Human *-- Brain //实线，头部为实菱形
     ```

   * 聚合

     ```cpp
     Company o-- Human //实线，头部为空菱形
     ```

   * 关联

     ```cpp
     Human --> Water //实线箭头
     ```

   * 依赖

     ```cpp
     Human ..> Cigarette  //虚线箭头
     ```

5. 例子

   ```
   @startuml
   interface Api{
   + test1()
   }
   class ImplA{
   + test1()
   }
   class ImplB{
   + test1()
   }
   class Factory{
   + createApi(cond:int):Api
   }
   class Client
   
   Api <|-- ImplA
   Api <|-- ImplB
   Factory ..> Api
   Factory ..> ImplA
   Factory ..> ImplB
   Client --> Api
   Client ..> Factory
   @enduml
   ```

   

