# QT

## QList

```cpp
/*QList使用运算符将项目添加到列表*/
QList<QString> list;
list<<"one"<<"two"<<“three”;
//list:["one","two","three"]
```

## QTGui

![img](https://img-blog.csdn.net/20150312141226941?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVsaWZhbmdqaWF5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20180826212435104?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Rxc18xMjIw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 简单的创建QMainWindow

```cpp
//mainwindow.h
#ifndef MAINWINDOW_H
#define MAINWINDOW_H
 
#include <QtGui/QMainWindow>
 
class QAction;
class QMenu;
 
class MainWindow : public QMainWindow
{
    Q_OBJECT
protected:
    QAction *fileOpenAction;  //定义一个动作
    QMenu *fileMenu;  //定义一个菜单
    
    QToolBar *fileToolBar;  //定义一个工具栏
public:
    MainWindow(QWidget *parent = 0);
    ~MainWindow();
public slots: //C++槽机制，很多信号可以与其相关联。当与其关联的信号被发射时，这个槽就会被调用。
	void slotOpenFile(); //在这里设置打开文件的操作
};
#endif // MAINWINDOW_H
```

```cpp
//mainwindow.cpp
#include "mainwindow.h"
#include <QMenu>
#include <QMenuBar>
#include <QToolBar>
#include <QAction>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{
    fileOpenAction = new QAction(tr("Open"),this);  //初始化动作
    connect(fileOpenAction, SIGNAL(triggered()), this, SLOT(slotOpenFile()));//将action(fileOpenAction)与slotOpenFile相关联。   
        
    fileMenu = menuBar()->addMenu(tr("File"));  //将File菜单添加到菜单栏上，menuBar()得到QMainWindow固有的菜单属性，tr("File")表明添加什么菜单，返回菜单对象的指针给fileMenu
    fileMenu->addAction(fileOpenAction);  //将动作添加到File菜单上
        
    fileToolBar = addToolBar(tr("File"));  //添加工具栏到顶层窗口中
    fileToolBar->addAction(fileOpenAction);  //为新建的工具栏添加动作
}
 
MainWindow::slotOpenFile()
{
    QMessageBox msgBox;
    msgBox.setText(tr("File Open"));
    msgBox.exec();
}
MainWindow::~MainWindow()
{
    
}
```

### ui文件与QT代码的联系

```cpp
//mywidget.h
#ifndef MYWIDGET_H
#define MYWIDGET_H

#include <QWidget>

namespace Ui{ //命名空间的作用：提前告诉编译器，此命名空间在别处定义
    class MyWidget;
}

class MyWidget : public QWidget{
    Q_OBJECT
public:
    explict MyWidget(QWidget *parent = 0); //explicit关键字的作用就是防止类构造函数的隐式自动转换，只能显示调用.explicit关键字只对有一个没有默认值的参数或只有一个参数的类构造函数有效。我不知道explict在这里有什么用？
    ~MyWidget();
private:
    Ui::MyWidget *ui;
}
```

```cpp
//mywidget.cpp
#include "mywidget.h"
#include "ui_mywidget.h" //写好的mywidget.ui自动转化而成的头文件（Qt Creator 用 uic 工具把 ui 文件的内容转换成 C++ 代码），Ui::MyWidget在此定义

MyWidget::MyWidget(QWidget *parent):
	QWidget(parent),
	ui(new Ui::MyWidget)
{
     ui->setuiUi(this);  
}
MyWidget::~MyWidget(){
    delete ui;
}
```

```cpp
//ui_mywidget.h
//...
class Ui_MyWidget{/*...*/};

namespace Ui{ //定义Ui命名空间，主要为了防止命名冲突
    class MyWidget: public Ui_MyWidget{};
}
```

### QWidget

1. ``QWidget(QWidget *parent = 0, Qt::WindowFlags f = 0);  ``,参数 f 是构造窗口的标志，主要用于控制窗口的类型和外观等

2. 独立窗口  

3. 一个窗口有两套几何参数，一套是窗口外边框所 占的矩形区域，另一套是窗口客户区所占的矩形区域。所谓窗口客户区就是窗口中去除边框和标题栏用来显示内容的区域。 坐标全部是外边框几何参数，而大小全部是客户区几何参数。

4. 可见性与隐藏 

   ```void hide(); ```

5. 窗口状态  

   独立窗口有正常、全屏、最大化、最小化几种状态

   ``void setWindowState(Qt::WindowStates windowState);      // 设置窗口状态,这里取值可以用 “按位或” 的方式组合起来使用  ``  	

6. 使能

    处于使能状态的窗口才能处理键盘和鼠标等输入事件

7. 激活状态

   当有多个独立窗口同时存在时，只有一个窗口能够处于激活状态。系统产生的键盘、鼠标等输入事件将被发送给处于激活状态的窗口

8. 焦点

     焦点用来控制同一个独立窗口内哪一个部件可以接受键盘事件，同一时刻只能有一个部件获得焦点

9. 事件

   QWidget 类能够处理类型丰富的事件

## QT对象间父子关系

Qt对象间可以存在父子关系，即每个对象都保存有它的所有子对象的指针，由一个链表保存起来；每个对象都包含一个指向它父对象的指针。所以当我们为一个Qt对象指定它的父对象时，它的父对象就会将这个子对象加入到自己的子对象链表中，同时该对象会保存指向其父对象的指针。 

## QT类

### QObject类

参考 <http://www.kuqin.com/qtdocument/qobject.html#connect>

#### connect

```cpp
bool QObject::connect(const QObject* sendr, const char* signal, const QObject* receiver, const cahr* member);//static

你必须在说明signal和member的时候使用SIGNAL()和SLOT()两个宏，例如：
connect(ui->buttonBox, SIGNAL(accepted()), this, SLOT(CreatePath()));
```

### QActionGroup类

```cpp
alignmentGroup = new QActionGroup(this);
alignmentGroup->addAction(leftAlignAct);
alignmentGroup->addAction(rightAlignAct);
alignmentGroup->addAction(justifyAct);
alignmentGroup->addAction(centerAct);
leftAlignAct->setChecked(true);
```

action group默认是互斥的；它确保在同一时刻只有一个action会被选中。如果你想创建一个action group而不想时它们是互斥关系，那么你可以通过调用setExclusive(false)来关闭互斥关系。

### QAction类

#### 属性

1. checkable: bool

   可检查的动作是具有开/关状态的动作。 例如，在文字处理器中，粗体工具栏按钮可以打开或关闭。 不是切换操作的操作是命令操作; 一个命令动作就简单地执行，例如， 文件保存。 默认情况下，此属性为false。

   在某些情况下，一个切换动作的状态应该取决于其他状态。 例如，“左对齐”，“中心”和“右对齐”切换操作是互斥的。 要实现独占切换，请将QActionGroup :: exclusive属性设置为true的相关切换操作添加到QActionGroup。

```cpp
[signal] void QAction::triggered(bool checked = false);
/*该信号在用户激活动作时发出; 例如，当用户单击菜单选项，工具栏按钮或按动作的快捷键组合时，或者在调用trigger（）时
值得注意的是，当setChecked（）或toggle（）被调用时它不会被发射。
*/
```

### QTreeView类

```cpp
void MainWindow::InitTree()
{
    //1，构造Model，这里示例具有3层关系的model构造过程
    QStandardItemModel* model = new QStandardItemModel(ui->treeView);
    model->setHorizontalHeaderLabels(QStringList()<<QStringLiteral("序号") << QStringLiteral("名称"));     //设置列头
    for(int i=0;i<5;i++)
    {
        //一级节点，加入mModel
        QList<QStandardItem*> items1;
        QStandardItem* item1 = new QStandardItem(QString::number(i));
        QStandardItem* item2 = new QStandardItem(QStringLiteral("一级节点"));
        items1.append(item1);
        items1.append(item2);
        model->appendRow(items1);
 
        for(int j=0;j<5;j++)
        {
            //二级节点,加入第1个一级节点
            QList<QStandardItem*> items2;
            QStandardItem* item3 = new QStandardItem(QString::number(j));
            QStandardItem* item4 = new QStandardItem(QStringLiteral("二级节点"));
            items2.append(item3);
            items2.append(item4);
            item1->appendRow(items2);
 
            for(int k=0;k<5;k++)
            {
                //三级节点,加入第1个二级节点
                QList<QStandardItem*> items3;
                QStandardItem* item5 = new QStandardItem(QString::number(k));
                QStandardItem* item6 = new QStandardItem(QStringLiteral("三级节点"));
                items3.append(item5);
                items3.append(item6);
                item3->appendRow(items3);
            }
        }
    }
    //2，给QTreeView应用model
    ui->treeView->setModel(model);
}
```

## QT信号槽机制

### 1

signal与slot是相互链接起来的，slot函数在每次signal函数被emit时会被调用。

声明信号，跟函数不一样，不需要定义

```c++
class myClass{
signals:
	void mySignal();
	void mySignal(int x);
	void mySignalParam(int x,int y);
public slots:
    void mySlot();
	void mySlot(int x);
	void mySlot(int x,int y);
}
```

连接信号signal和槽函数slot:``connect(sender, SIGNAL(mySignal()), receiver, SLOT(mySlot()));``

信号和槽函数必须有着相同的参数类型及顺序，这样信号和槽函数才能成功连接。

在用到的地方发送信号：``emit mySignal()；``

```cpp
QObject * QObject::sender () const [protected]
//sender () 函数返回 信号发出者 的 QObject型指针。对QObject型指针进行强制转换得到需要的。

QAction* action = qobject_cast<QAction*> ( sender() );
```

```cpp
connect（aButton，SIGNAL（clicked（）），SIGNAL（aSignal（）））;
//按钮的单击事件产生的信号clicked（）与另外一个信号aSignal（）进行了关联。这样一来，当信号clicked（）被发射时，信号aSignal（）也接着被发射
```

**原来复杂的类间数据传输是通过复杂的连接信号与槽来实现的**

### 2

以上的语法是QT4的实现，在QT5中，有不同的实现，可以解决信号重载的问题。

定义：QObject::connect(*sender,&signal,*receiver,&slot);

```cpp
//例子1
void (QComboBox::*fun)(int) = &QComboBox::currentIndexChanged;
QObject::connect(comboBox,fun,*receiver,&slot);
/*发送者是类型为QComboBox*的comboBox,fun是函数指针，它已经指定了重载函数的哪种参数
*/
```

```cpp
//例子2
//QSpinBox 类中的信号函数
void valueChanged(int);
void valueChanged(const QString &);

connect(ui->spinBox, static_cast<void(QSpinBox::*)(int)>(&QSpinBox::valueChanged), this, &TimeLine::SendSignalsFrameChanged);
```

```cpp
//Qt4版本,与Qt5使用函数指针效果等价,但是不推荐用。
connect(&subWnd,SIGNAL(MySignal()),this,SLOT(Sub()));

connect(&subWnd,SIGNAL(MySignal(int,QString)),this,SLOT(TextSignal(int,QString)));
```



# MITK

```cpp
//创建一个DataNode
sv4guiPath::Pointer path = sv4guiPath::New();
mitk::DataNode::Pointer pathNode = mitk::DataNode::New();
pathNode->SetData(path);
```

