

# MITK框架

* Insight工具包（ITK），它提供了登记和分割算法，但不是设计的可视化和交互。
* 可视化工具包（VTK），它提供了强大的可视化功能和作用如采摘方法，旋转底层支持，移动和缩放对象。
* 常用工具包（CTK），以DICOM支持插件框架。
* Qt跨平台应用程序和UI框架（Qt）作为UI和应用程序支持的框架。

# Developing with the MITK Application Framework

## 重要的概念

view，editor，perspective，workbench window，workbench

perspective：IPerspectiveFactory的实现类Perspective，Perspective是工作台所有**已打开的视图和编辑器**的容器。每个工作台只有一个透视图，我们可将相关功能统一透视图中。视图和编辑器不可在不同的透视图之间共享。透视图具有它的编辑器，视图，可设计它们执行不同的功能呢个。一个系统中也可以由多个透视图，透视图的个数取决于应用程序的复杂程度。

这些概念在SimVascular的GUI中都有体现，很好理解。

## a minimal BlueBerry application

SimVascular的sv4gui_Application文件中的源码与这里很像，估计就是定义了一个gui最顶层的workbench， it is the WorkbenchAdvisor class that creates the WorkbenchWindowAdvisor。

```cpp
//MinimalApplication.h
#ifndef MINIMALAPPLICATION_H_
#define MINIMALAPPLICATION_H_
// Berry
#include <berryIApplication.h>
// Qt
#include <QObject>
#include <QScopedPointer>
class MinimalWorkbenchAdvisor;

class MinimalApplication : public QObject, public berry::IApplication
{
  Q_OBJECT
  Q_INTERFACES(berry::IApplication)
public:
  MinimalApplication();
  ~MinimalApplication();
  QVariant Start(berry::IApplicationContext *context) override;
  void Stop() override;
};
#endif /*MINIMALAPPLICATION_H_*/
//我不知道这个start函数是怎么调用的，好像并不是直接start()，因为里面的参数不知道怎么传进去
```

```cpp
#include "MinimalApplication.h"

// Berry
#include <berryPlatformUI.h>
#include <berryQtWorkbenchAdvisor.h>
#include <QPoint>

class MinimalWorkbenchAdvisor : public berry::WorkbenchAdvisor
{
public:
  static const QString DEFAULT_PERSPECTIVE_ID;

  berry::WorkbenchWindowAdvisor *CreateWorkbenchWindowAdvisor(
    berry::IWorkbenchWindowConfigurer::Pointer configurer) override
  {
    // Set an individual initial size
    configurer->SetInitialSize(QPoint(600, 400));
    // Set an individual title
    configurer->SetTitle("Minimal Application");
    // Enable or disable the perspective bar
    configurer->SetShowPerspectiveBar(false);

    return new berry::WorkbenchWindowAdvisor(configurer);
  }

  QString GetInitialWindowPerspectiveId() override { return DEFAULT_PERSPECTIVE_ID; }
};

const QString MinimalWorkbenchAdvisor::DEFAULT_PERSPECTIVE_ID = "org.mitk.example.minimalperspective";

MinimalApplication::MinimalApplication()
{
}

MinimalApplication::~MinimalApplication()
{
}

QVariant MinimalApplication::Start(berry::IApplicationContext * /*context*/)
{
  QScopedPointer<berry::Display> display(berry::PlatformUI::CreateDisplay());

  QScopedPointer<MinimalWorkbenchAdvisor> wbAdvisor(new MinimalWorkbenchAdvisor());
  int code = berry::PlatformUI::CreateAndRunWorkbench(display.data(), wbAdvisor.data());

  // exit the application with an appropriate return code
  return code == berry::PlatformUI::RETURN_RESTART ? EXIT_RESTART : EXIT_OK;
}

void MinimalApplication::Stop()
{
}
/*
berry::WorkbenchWindowAdvisor controls the WorkbenchWindow layout:
*/
```

## MainWindow Layout: ViewerPerspective and DicomPerspective

见 <http://docs.mitk.org/nightly/MainWindowLayout.html>

```cpp
berry::WorkbenchWindowAdvisor *CustomViewerWorkbenchAdvisor::CreateWorkbenchWindowAdvisor(
  berry::IWorkbenchWindowConfigurer::Pointer configurer)
{
  return new CustomViewerWorkbenchWindowAdvisor(configurer);
}
```

可以在PreWindowOpen()中设置configurer

```cpp
void CustomViewerWorkbenchWindowAdvisor::PreWindowOpen()
{
  berry::IWorkbenchWindowConfigurer::Pointer configurer = this->GetWindowConfigurer();
  configurer->SetTitle("Spartan Viewer");
  configurer->SetShowMenuBar(false);
}
```

我发现：关于workbench的某些类（也就是用blueberry开发的GUI），比如：berry::IApplication和berry::IPerspective都要放在plugin.xml文件中，然后再在main函数中加载plugin.xml文件，可以运行其中的某些类的start()函数。

The customization of the window contents takes place within the ```CreateWindowContents``` method. That means, we can overwrite the superclass' CreateWindowContents method and lay out every widget of the main window individually. Given the method's [berry::Shell](http://docs.mitk.org/nightly/classberry_1_1Shell.html) parameter, we can extract the application's main window as QMainWindow using the [berry::Shell::GetControl()](http://docs.mitk.org/nightly/classberry_1_1Shell.html#a96fdf8989383d053872e168fcb66c4bb)-method:

```cpp
void CustomViewerWorkbenchWindowAdvisor::CreateWindowContents(berry::Shell::Pointer shell)
{
  // the all containing main window
  QMainWindow *mainWindow = static_cast<QMainWindow *>(shell->GetControl());
  mainWindow->setCentralWidget(CentralWidget);
  CentralWidgetLayout->addLayout(PerspectivesLayer);
  CentralWidgetLayout->addWidget(PageComposite);
  CentralWidget->setLayout(CentralWidgetLayout);
  PerspectivesLayer->addWidget(PerspectivesTabBar);
  PerspectivesLayer->addSpacing(300);
  PerspectivesLayer->addWidget(OpenFileButton);
  // for correct initial layout, see also bug#1654
  CentralWidgetLayout->activate();
  CentralWidgetLayout->update();
  this->GetWindowConfigurer()->CreatePageComposite(PageComposite);//将PageComposite作为workbench的page（默认除标题外的窗口的所有区域都是page，但是有一部分被PerspectivesTabBar和OpenFIleButton给占用了）
/*
在这里，有两个perspective，所以PerspectivesTabBar是和PageComposite垂直放在QMainWindow中，PageComposite就是blueberry中一个page，用来存放我们熟知的menubar,toolbar之类的东西。
CentralWidgetLayout是一个VBoxLayout对象，PerspectivesLayer是一个HBoxLayout对象。
*/
```



## Selection Service

![SelectionServiceDiagram.png](http://docs.mitk.org/2016.11/SelectionServiceDiagram.png)

blueberry用类似于广播机制的东西来在类与类之间传播数据。QmitkAbstractView可实现selection service。

在MITK中，可以把Data Node转化为一个控件。

```cpp
 QListWidgetItem *listItemDataNode1 = new 					       QListWidgetItem(QString::fromStdString(dataNode1->GetName()));

listItemDataNode1->setData(QmitkDataNodeRole, QVariant::fromValue(dataNode1));
```

```cpp
//SelectionViewMitk.h
// ***************************
//...
// ***************************
#include "ui_SelectionViewMitkControls.h" //SelectionViewMitkControls.cpp已经设置好m_SelectionList控件。我怀疑：SelectionViewMitkControls类不是关于Selection Service的类，所以又写了一个SelectionViewMitk类与其关联，来设置Selection Service。

class SelectionViewMitk : public QmitkAbstractView
{
  Q_OBJECT

public:
  static const std::string VIEW_ID;
  SelectionViewMitk();
protected:
  virtual void CreateQtPartControl(QWidget *parent) override;
  void SetFocus() override;
private:
  //! [MITK Selection Provider method]
  /** @brief Reimplementation of method from QmitkAbstractView that returns the data node
  *   selection model for the selection listener.
  */
  QItemSelectionModel *GetDataNodeSelectionModel() const override;
  //! [MITK Selection Provider method]
  Ui::SelectionViewMitkControls m_Controls;
  QWidget *m_Parent;
};
#endif /*SELECTIONVIEWMITK_H_*/
/*m_Controls的类型不是Ui::SelectionViewMitk而是Ui::SelectionViewMitkControls
*/
```

```cpp
//SelectionViewMitk.cpp
#include "SelectionViewMitk.h"

// qmitk Includes
#include <QmitkCustomVariants.h>
#include <QmitkEnums.h>

const std::string SelectionViewMitk::VIEW_ID = "org.mitk.views.selectionviewmitk";

SelectionViewMitk::SelectionViewMitk() : m_Parent(nullptr)
{
}

//In CreateQtPartControl() we can now lay out the view controls(view控件)。
void SelectionViewMitk::CreateQtPartControl(QWidget *parent)
{
  // create GUI widgets
  m_Parent = parent;
  m_Controls.setupUi(parent);

  //! [MITK Selection Provider data nodes]
  // create two data nodes and change there default name
  mitk::DataNode::Pointer dataNode1 = mitk::DataNode::New();
  dataNode1->SetName("DataNode 1");
  mitk::DataNode::Pointer dataNode2 = mitk::DataNode::New();
  dataNode2->SetName("DataNode 2");
  //! [MITK Selection Provider data nodes]

  //! [MITK Selection Provider listwidgetitems]
  // create two QListWidgetItems and name them after the create data nodes
  QListWidgetItem *listItemDataNode1 = new 					       QListWidgetItem(QString::fromStdString(dataNode1->GetName()));
  QListWidgetItem *listItemDataNode2 = new QListWidgetItem(QString::fromStdString(dataNode2->GetName()));

  // set the data of created QListWidgetItems two the informations of the data nodes
  listItemDataNode1->setData(QmitkDataNodeRole, QVariant::fromValue(dataNode1));
  listItemDataNode2->setData(QmitkDataNodeRole, QVariant::fromValue(dataNode2));

  // add the items to the QListWidget of the view
  m_Controls.m_SelectionList->addItem(listItemDataNode1);
  m_Controls.m_SelectionList->addItem(listItemDataNode2);
  //! [MITK Selection Provider listwidgetitems]

  // set selection mode to single selection
  m_Controls.m_SelectionList->setSelectionMode(QAbstractItemView::SingleSelection);

  // pre-select the first item of the list
  m_Controls.m_SelectionList->setCurrentRow(0);

  m_Parent->setEnabled(true);
}

void SelectionViewMitk::SetFocus()
{
}

//! [MITK Selection Provider method implementation]
QItemSelectionModel *SelectionViewMitk::GetDataNodeSelectionModel() const
{
  // return the selection model of the QListWidget
  return m_Controls.m_SelectionList->selectionModel();
}
//! [MITK Selection Provider method implementation]
```

```cpp
//ListenerViewMitk.h
#ifndef LISTENERVIEWMITK_H_
#define LISTENERVIEWMITK_H_

// QMitk includes
#include <QmitkAbstractView.h>

// berry includes
#include <berryIWorkbenchWindow.h>

// Qt includes
#include <QString>

// ui includes
#include "ui_ListenerViewMitkControls.h"

/**
 * \ingroup org_mitk_example_gui_selectionservicemitk
 *
 * \brief This BlueBerry view is part of the BlueBerry example
 * "Selection service MITK". It creates two radio buttons that listen
 * for selection events of the Qlistwidget (SelectionProvider) and
 * change the radio button state accordingly.
 *
 * @see SelectionViewMitk
 *
 */
class ListenerViewMitk : public QmitkAbstractView
{
  Q_OBJECT

public:
  static const std::string VIEW_ID;

  ListenerViewMitk();

protected:
  void CreateQtPartControl(QWidget *parent) override;

  void SetFocus() override;

  //! [MITK Selection Listener method]
  /** @brief Reimplemention of method from QmitkAbstractView that implements the selection listener functionality.
   * @param part The workbench part responsible for the selection change.
   * @param nodes A list of selected nodes.
   *
   * @see QmitkAbstractView
   *
   */
  virtual void OnSelectionChanged(berry::IWorkbenchPart::Pointer part,
                                  const QList<mitk::DataNode::Pointer> &nodes) override;
  //! [MITK Selection Listener method]

private Q_SLOTS:

  /** @brief Simple slot function that changes the selection of the radio buttons according to the passed string.
   * @param selectStr QString that contains the name of the slected list element
   *
   */
  void ToggleRadioMethod(QString selectStr);

private:
  Ui::ListenerViewMitkControls m_Controls;

  QWidget *m_Parent;
};

#endif /*LISTENERVIEWMITK_H_*/
```

```cpp
//ListenerViewMitk.cpp
#include "ListenerViewMitk.h"

// Mitk includes
#include "mitkDataNodeObject.h"

const std::string ListenerViewMitk::VIEW_ID = "org.mitk.views.listenerviewmitk";

ListenerViewMitk::ListenerViewMitk() : m_Parent(nullptr)
{
}

void ListenerViewMitk::CreateQtPartControl(QWidget *parent)
{
  // create GUI widgets
  m_Parent = parent;
  m_Controls.setupUi(parent);

  m_Parent->setEnabled(true);
}

void ListenerViewMitk::ToggleRadioMethod(QString selectStr)
{
  // change the radio button state according to the name of the selected data node
  if (selectStr == "DataNode 1")
    m_Controls.radioButton->toggle();
  else if (selectStr == "DataNode 2")
    m_Controls.radioButton_2->toggle();
}

void ListenerViewMitk::SetFocus()
{
}

//! [MITK Selection Listener method implementation]
void ListenerViewMitk::OnSelectionChanged(berry::IWorkbenchPart::Pointer /*part*/,
                                          const QList<mitk::DataNode::Pointer> &nodes)
{
  // any nodes there?
  if (!nodes.empty())
  {
    // get the selected node (in the BlueBerry example there is always only one node)
    mitk::DataNode::Pointer dataNode = nodes.front();
    // pass the name of the selected node to method
    ToggleRadioMethod(QString::fromStdString(dataNode->GetName()));
  }
}
//! [MITK Selection Listener method implementation]
```

# 插件开发——应用运行顺序

以下内容都是从RCP开发的相关资料中l学习的，不知为何，用BlueBerry和CTK做应用程序开发和RCP开发几乎一模一样。

```
ctkPluginActivator：插件激活器，在插件启动时（所有类调用之前）调用其start()方法，在插件卸载时调用其stop()方法。

berry::IApplication：应用程序管理类，也算是应用程序的入口类。

berry::ActionBarAdvisor：菜单、工具栏、状态栏管理类。

berry::WorkbenchAdvisor：应用工作台管理类。

berry::WorkbenchWindowAdvisor：应用工作台窗口管理类，可以进行窗口相关的初始化工作，例如程序启动后窗口最大化。
```

SimVascular的程序运行大致过程：

1. **插件激活**，以sv4gui_Main中的代码为例

   ``app.setProperty(ctkPluginFrameworkLauncher::PROP_PLUGINS, pluginsToStart);``

   估计``app.run()``后自动就运行``pluginsToStart中的所有ctkPluginActivator``的start()函数。

2. **应用启动**，应用程序会查找plugin.xml文件中扩展标签中定义的应用扩展（"org.sv.qt.application"），也就是运行sv4gui_Application的start()函数

   ```cpp
   QVariant sv4guiApplication::Start(berry::IApplicationContext* /*context*/)
   {
     QScopedPointer<berry::Display> display(berry::PlatformUI::CreateDisplay());
   
     QScopedPointer<sv4guiAppWorkbenchAdvisor> wbAdvisor(new sv4guiAppWorkbenchAdvisor());
     int code = berry::PlatformUI::CreateAndRunWorkbench(display.data(), wbAdvisor.data());
   
     // exit the application with an appropriate return code
     return code == berry::PlatformUI::RETURN_RESTART ? EXIT_RESTART : EXIT_OK;
   }
   ```

3. **工作台启动**，工作台管理类中做了两件事：创建工作台窗口管理类、返回初始化的透视图ID。如``sv4gui_AppWorkbenchAdvisor``中的函数声明。

   ```cpp
   berry::WorkbenchWindowAdvisor* CreateWorkbenchWindowAdvisor(berry::IWorkbenchWindowConfigurer::Pointer configurer) override;
   
   QString GetInitialWindowPerspectiveId() override;
   ```

4. **加载perspective**，应用将根据工作台管理类中返回的透视图ID，从plugin.xml文件中查找对应的透视图，执行相应的透视图类中的代码。如plugin.xml中的"org.sv.application.defaultperspective"
5. **工作台窗口初始化**，在此处将初始化窗口信息（如初始化大小、是否显示工具栏、菜单栏、状态栏等）、创建动作条管理类（berry::ActionBarAdvisor）。

6. **动作条管理**

# MITK的类

## BlueBerry

### IWorkbenchWindow类

``sv4gui_WorkbenchWindowAdvisor``

```cpp
	berry::IWorkbenchWindow::Pointer window = this->GetWindowConfigurer()->GetWindow();
    QMainWindow* mainWindow = qobject_cast<QMainWindow*> (window->GetShell()->GetControl());
/*在IWorkbenchAdvisor类中可通过这种方式获取到IWorkbenchWindow以及QMainWindow
*/

bool imageNavigatorViewFound = window->GetWorkbench()->GetViewRegistry()->Find("org.mitk.views.imagenavigator");
/*window是berry::IWorkbenchWindow类型，通过这行代码可以找到注册的view
*/

berry::IViewPart::Pointer imageNavigatorView =
                window->GetActivePage()->FindView("org.mitk.views.imagenavigator");
/*通过view id可得到view。
*/
```

### PlatformUI类

这个类不能被实例化，可以很神奇的得到IWorkbenchWindow或者其他的。

```cpp
void sv4guiWorkbenchWindowAdvisorHack::SafeHandleNavigatorView(QString view_query_name)
{
    berry::IWorkbench* wbench = berry::PlatformUI::GetWorkbench(); //通过PlatformUI得到IWorkbench
    if( wbench == nullptr )
        return;

    berry::IWorkbenchWindow::Pointer wbench_window = wbench->GetActiveWorkbenchWindow(); //通过PlatformUI得到IWorkbenchWindow
    if( wbench_window.IsNull() )
        return;

    berry::IWorkbenchPage::Pointer wbench_page = wbench_window->GetActivePage(); //得到IWorkbenchPage
    if( wbench_page.IsNull() )
        return;

    auto wbench_view = wbench_page->FindView( view_query_name );

    if( wbench_view.IsNotNull() )
    {
        bool isViewVisible = wbench_page->IsPartVisible( wbench_view );
        if( isViewVisible )
        {
            wbench_page->HideView( wbench_view );
            return;
        }

    }

    wbench_page->ShowView( view_query_name ); //通过view id来展示view
}
```

``berry::PlatformUI::GetWorkbench()->Close();``关闭整个程序。