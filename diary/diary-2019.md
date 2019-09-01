# 2019/8/5

### 青春痘大小测量

#### 要学习的东西

* 深度卷积网络
* data augumentation
* 学会用框架搭建网络

# 2019/8/6

### 青春痘大小测量

1. U-Net的数据集包括类型为np.float32的训练图片和标签，训练图片的元素的值在``[0.0, 1.0]``，标签图片中像素只有1.0和0.0两种值。

2. 训练过程有两个关键词：steps和epoch。

   拥有越高性能的GPU，则可以设置越大的batch_size值。根据现有硬件，我们设置了每批次输入50-100张图像。参数steps_per_epoch是通过把训练图像的数量除以批次大小得出的。例如，有100张图像且批次大小为50，则steps_per_epoch值为2。

3. <https://github.com/FENGShuanglang/unet>中U-Net源码

   data.py中的trainGenerator()是生成器，用于一次性生成batch_size数量的(image, label)组合，然后喂到(model.fit_generator, model是keras.models.Model的对象) 模型中进行训练。

### 血管展示（SimVascular）

1. paths along the vessels of interest need to be specified first (See [Path Planning](http://simvascular.github.io/docsModelGuide.html#modelingPathPlanning)).

   * It’s generally helpful if there is some overlap in the paths. 意思应该是如果两个血管有交点，描述两个血管的path也应该有相同的元素。例如：path1(1,2,3,4) path2(4,5,6,7)表示两个血管段在4点是交点。

   * .paths文件的输入格式

     ```
     #  Geodesic path file version 2.0 (from guiPP)
     global gPathPoints
     #设置第一个血管段的名字
     set gPathPoints(1,name) {aorta1}
     #设置control point（交点或端点）
     set gPathPoints(1,0) { 28.9357898 14.2210450 19.7754081}
     set gPathPoints(1,1) { 27.2156913 14.3360356 22.9297289}
     #设置第一个血管段的名字
     set gPathPoints(2,name) {aorta2}
     set gPathPoints(2,0) { 27.2156913 14.3360356 22.9297289}
     set gPathPoints(2,1) { 25.2170969 15.5868497 25.1305433}
     #设置第一个血管段的名字
     set gPathPoints(3,name) {aorta3}
     set gPathPoints(3,0) { 27.2156913 14.3360356 22.9297289}
     set gPathPoints(3,1) { 26.1320274 13.6633870 26.2691703}
     #设置第一个血管段的path， t和tx后面的不可没有，但可以随便设，因为这是export后的项
     set gPathPoints(1,numSplinePts) {2}
     set gPathPoints(1,splinePts) [list \
     	{p (28.9357898 14.2210450 19.7754081) t(0,0,0) tx(0,0,0)} \
     	{p (27.2156913 14.3360356 22.9297289) t(0,0,0) tx(0,0,0)} \
     	]
     #设置第二个血管段的path
     set gPathPoints(2,numSplinePts) {2}
     set gPathPoints(2,splinePts) [list \
     	{p (27.2156913 14.3360356 22.9297289) t(0,0,0) tx(0,0,0)} \
     	{p (25.2170969 15.5868497 25.1305433) t(0,0,0) tx(0,0,0)} \
     	]
     #设置第三个血管段的path
     set gPathPoints(3,numSplinePts) {2}
     set gPathPoints(3,splinePts) [list \
     	{p (27.2156913 14.3360356 22.9297289) t(0,0,0) tx(0,0,0)} \
     	{p (26.1320274 13.6633870 26.2691703) t(0,0,0) tx(0,0,0)} \
     	]
     ```

2. 找出SVProject1/Paths(右击)/Import Paths 的源码

   

# 2019/8/8

### 青春痘大小测量

1. 如何安装keras（anaconda下）?

   ```python
   pip install keras
   ```

   这也太简单了

2. 运行<https://github.com/FENGShuanglang/unet>的源码出现了错误

   * 见[importError: cannot import name '_validate_lengths'](https://blog.csdn.net/weixin_44508906/article/details/87347875)

     这是由于numpy版本更新导致的。

   * 执行``merge([drop4,up6], mode='concat', concat_axis=3)``时报错:

     ``typeError: module is not callable``

     原因是keras版本更新到2.0之后的问题，改为

     ``concatenate([drop4,up6], axis=3)``即可解决问题。

3. * 我觉得用物体检测的方法没法获得图片中硬币的位置，因为在imagenet上找不到硬币的bounding box，必须通过其他方法。比如：blob分析之硬币检测。

   * 源码中的图片生成器自动把图片转换为灰度图，归一化，256x256的形式。

   * **评价**：

     这个代码没有指定n折交叉验证（我也不知道对于图片分割的数据集，怎么实现n折交叉验证），也没有用dice coefficient loss这个我在论文中看到的评价指标。

4. 训练图片的大小不符合网络的输入怎么办，测试图片的大小不符合网络的输入怎么办？



# 2019/8/9

### SCI期刊

SCI有两个分区规则：JCR分区和[中科院](https://www.baidu.com/s?wd=中科院&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)的分区

JCR分区根据[影响因子](https://www.baidu.com/s?wd=影响因子&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)（IF值），某一个学科的所有期刊都按照上一年的[影响因子](https://www.baidu.com/s?wd=影响因子&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)降序排列，然后平均4等分(各25%)，分别是Q1，Q2，Q3，Q4
[中科院](https://www.baidu.com/s?wd=中科院&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)分区的方法：一区刊：各类期刊三年平均[影响因子](https://www.baidu.com/s?wd=影响因子&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)的前5%，二区刊：前6% ～ 20%，三区刊：前21% ～ 50%，四区刊：后51%～100%  

因此，IEEE Access按照JCR分区（我们的computer区）算Q1，按照中科院的分区算二区。

# 2019/8/10

### SimVascular

1. 先写.paths文件，在SV Data Manager/SVProject中右击Paths, import 进去

2. smooth: SV Path Planning/Smooth

3. segmentation

   在血管3D建模中不是要血管半径吗，但是在SimVascular的输入中（输入.paths文件）并没有指定半径，而是在segmentation中画contours时，才指定的。

   SVProject的输入Images是3D文件（vti格式），因此可以进行segmentation来分割出血管腔。但是我们不能做分割，只能以path point为圆心和给定的半径来画圆，然后生成segmentation的文件格式。

![InkedO2@`UG~RVVKM}KEHR{QUXF6_LI](C:\Users\周华辉\Desktop\InkedO2@`UG~RVVKM}KEHR{QUXF6_LI.jpg)

![0.jpg](https://github.com/zhouhuahui/image/blob/master/0.jpg?raw=true)

就像这张图所示，我猜测：左上图相当于切面图，就是根据左上图来分割（白色区域，就是血管腔），但还需要把分割的contour画到CD线垂直的平面，这就是为什么在生成.paths或.pth文件时为什么会有很多和斜率有关的项，应该是为了这种操作服务。这种方法比单纯地在2D图像上估计血管半径来的准确的多。

因此下一步的工作就是找到画contour的函数以及根据很多个contour生成文件的函数。

我可以用SV 2D Segmentation/Circle来简单画个圆形contour。

4. * ``sv4gui_PathIO``可以从文件中读取所有的path

     但需要插件mitk, 这个文件中的类继承自``mitk::AbstractFileIO``（位于``mitkAbstractFileIO.h``）

     ```ReadFile()函数```

     ```cpp
     sv4guiPath::Pointer path = sv4guiPath::New(); //sv4guiPath继承自mitk::BaseData, 我怀疑mitk::BaseData中有嵌套类定义Pointer
     //...
     sv3::PathIO* reader = new sv3::PathIO();
     sv3::PathGroup* svPathGrp = reader->ReadFile(fileName); //读取了所有path
     //...
     std::vector<mitk::BaseData::Pointer> result;
     result.push_back(path.GetPointer());
     return result; //sv4guiPathIO类中的ReadFile()返回这个，说明读取的所有path还会放在某个固定的类中，这个函数只是中间函数（答案是sv4guiPath类）
     ```

     ``Write()函数``

     ```cpp
     const sv4guiPath* path = dynamic_cast<const sv4guiPath*>(this->GetInput());//通过这种方式获得所有path
     //我猜测:GetInput这个函数是来源于mitk::AbstractFileIO类的静态方法。
     //...
     for(int t=0;t<path->GetTimeSize();t++){
     	sv4guiPathElement* pe=path->GetPathElement(t);
     	sv3::PathElement* svPe=static_cast<sv3::PathElement*>(pe);
     	this->sv3::PathIO::WritePath(svPe,timestepElement); 
     }//export到.pth文件中，与sv3::PathIO的Write()函数不相关，但都可以用。
     ```

   * ``sv3_PathIO``中的``sv3::PathIO``类

     ```cpp
     int PathIO::Write(std::string fileName, PathGroup* pathGrp){}
     //export出.pth文件的底层函数
     ```

   * ``sv4gui_PathLoadAction``

     ```cpp
     if(fileName.endsWith(".paths"))
             {
                 std::vector<mitk::DataNode::Pointer> newNodes=sv4guiPathLegacyIO::ReadFile(fileName);
                 for(int i=0;i<newNodes.size();i++)
                 {
                     mitk::DataNode::Pointer exitingNode=m_DataStorage->GetNamedDerivedNode(newNodes[i]->GetName().c_str(),selectedNode);
                     if(exitingNode){
                         MITK_WARN << "Path "<< newNodes[i]->GetName() << " Already Created","Please use a different path name!";
                     }
                     m_DataStorage->Add(newNodes[i],selectedNode);
                 }
             }
     //说明所有的path均保存在mitk::DataStorage::Pointer m_DataStorage中，而且export到.paths文件中也是从m_DataStorage开始。
     ```

   * 总结下来：要抽取源码实现.path文件的导入必须要有：sv4gui_PathLoadAction以及它包含的所有文件。但导出要有sv4gui_PathLegacySaveAction, 导入后数据在sv4gui_PathLoadAction中的m_DataStorage中，又不在sv4gui_PathLegacySaveAction中的m_DataStorage中。

# 2019/8/10

### SimVascular

对于.pth文件中的tangent项我明白了，就是两个path point的形成的向量单位化后的东西，但rotation是什么鬼，我觉totation得x=0, y和tangent的z很想, z和tangent的y很像。

1. 用circle画contour

   * ``sv4gui_ContourCircle``文件

   ```cpp
   void sv4guiContourCircle::CreateContourPoints()
   {
       m_PlaneGeometry->Map(GetControlPoint(0), centerPoint );
       m_PlaneGeometry->Map(GetControlPoint(1), boundaryPoint ); //原来两个control point就是圆的的中心点和边界点
       //...
       //属性m_ContourPoints保存一个path上的一个pathpoint的contour的所有contour point
   } //画一个contour的底层函数
   ```

   由``sv4guiContourCircle::CreateContourPoints``函数只能创造一个contour且保存在它实例的对象中，要实现从contour到contourGroup再到DataStorage的变换（要么从源码中抽取，要么自己写）。

   一个contourGroup是一个path上的所有contour。

   * 在mitk::DataStorage::Pointer m_DataStorage（SV中存储数据的最高层结构）添加元素时，为什么这样?

   ```cpp
   m_DataStorage->Add(newNodes[i],selectedNode); //存newNodes[i]不就好了吗，为什么还存后面的
   ```

   * ```sv4gui_ContourGroupCreateAction```文件

   ```cpp
   void sv4guiContourGroupCreateAction::Run(const QList<mitk::DataNode::Pointer> &selectedNodes){
       //...
   	m_ContourGroupCreateWidget=new sv4guiContourGroupCreate(m_DataStorage, 		selectedNode, timeStep); //Create Contour Group的核心语句，但是不知道传进来的参数selectedNodes是什么意思，以及如何剔除掉sv4guiContourGroupCreate()函数中UI操作部分。
       //...
   }
   ```

2. * 我觉得入手点应该在org.sv.gui.application和org.sv.gui.qt.datamanager

   * 在项目刚打开之初，有以下类间的关系：

     sv4guiCloseProjectAction和sv4guiPathEdit有关系

# 2019/8/12

### SimVascular

*sv4gui_FileOpenProjectAction*

```cpp
void sv4guiFileOpenProjectAction::Run(){
    //...
    ctkPluginContext* context = sv4guiApplicationPluginActivator::getContext();
    //...
    mitk::StatusBar::GetInstance()->DisplayText("Opening SV project...");//通过单例模式（估计statusBar只有一个对象）来操作UI界面。
    //...
    sv4guiProjectManager::AddProject(dataStorage, projName,projParentDir,false);//由context可得到dataStorage。dataStorage在得到项目路径之前就已经赋值完毕，说明dataStorage并不是项目中包含的数据，而是另一种东西。
}
```

*sv4gui_MainWindow* 我总觉的sv4gui_MainWindow在编译过程被抛弃了

```cpp
svMainWindow::svMainWindow(QWidget *parent) :
    QMainWindow(parent)
{
    //...
    svDisplayEditor* displayEditor=new svDisplayEditor;
    m_DisplayWidget=displayEditor->GetDisplayWidget();
    sv4guiApplication::application()->setDisplayWidget(m_DisplayWidget);
    //我想sv4guiApplication::application()返回的是一个对象，这个对象有关UI界面
}
```

1. * 原来对于``org.sv.gui.qt.pathplanning``里的很多类(比如sv4guiPathCreate)都是一个QWidget，也有UI设置。sv4guiPathCreate类里面有关于create path的所有槽(slot)，其他类也是这样。

     那么MainWindow在哪呢？这些QWidget是在哪里实例化的？``sv4guiPathPlanningPluginActivator``类为什么和``sv4guiPathCreateAction``之类的类有关联。

   * sv4guiApplicationPluginActivator类与sv4guiApplication类，sv4guiMain，sv4guiMainWindow,，sv4guiDefaultPerspective类很迷

   * berry，mitk，Qmitk，ctk是干嘛的？

2. 我发现sv4guiMain类类似于mitk官网上的简单workbench程序，而sv4guiMain用到的sv4guiMitkApp类就是继承于BaseApplication。下面是别人的简单workbench

   ```cpp
   #include <mitkBaseApplication.h>
   #include "berryLog.h"
   #include <QVariant>
   
   int main(int argc, char** argv)
   {
   	mitk::BaseApplication app(argc, argv);
   
   	// arguments are here
   	for (int i = 1; i < argc; ++i)
   	{
   		QString arg(QString::fromLocal8Bit(argv[i]));
   		BERRY_INFO << "arg :  " << arg;
   	}
   
   	// does not change nothing
   	app.setProperty(mitk::BaseApplication::PROP_TESTAPPLICATION, "id.of.the.test.plugin");
   
   	// does not change nothing
   	app.setProperty(mitk::BaseApplication::ARG_TESTPLUGIN, "id.of.the.test.plugin");
   	
   	// if I forget it, it does not work
   	app.setProperty(mitk::BaseApplication::PROP_APPLICATION, "org.blueberry.uitest");
   
   	// Run the workbench
   	return app.run();
   }
   ```

   

3. 从别人那抄的经验

   ```
   先利用官方文档尝试多写插件，多写module，实现plugin和module的方式最好能遵循MITK准则。比如，mapper只负责渲染，interactor负责交互，从basedata继承下来的数据放在module中，basedata交由datanode管理，module到插件通过观察者通知，插件到module通过直接调用的方式，basedata序列化通过继承 抽象的reader和writer等等，虽然可能一个需求的实现方法有很多，尽量选用最符合框架开发者思想的方法。
   
   多看源码，这里说的源码不是从main开始看，而是 看一种数据结构，比如mitksurface，先看该类实现，再看该类对应的2D mapper和3Dmapper，再看mitkImage和mitkPointset。多看几遍就能有个大概的想法。
   
   尝试从basedata继承，写一种数据结构，实现该数据结构的 interactor，mapper,序列化等等，可以参考multilabel和contourmodel模块。
   
   结合Eclipse插件开发手册，多看看blueberry的源码，理解 perspective view ,workbench workbenchwindow,workbenchwindowadvisor等等类的意义。
   ```

# 2019/8/13

1. 什么叫插件？插件是怎么调用的？

2. * 在sv4guiMain中有``app.setProperty(ctkPluginFrameworkLauncher::PROP_PLUGINS, pluginsToStart);`` pluginsToStart列表中有org_sv_gui_qt_pathplanning文件夹，org_sv_gui_qt_pathplanning文件夹中有sv4gui_PathPlanningPluginActivator文件。

   * org.sv.gui.qt.pathplanning文件夹下的files.cmake和Makefile有提到sv4gui_PathPlanningPluginActivator

   * 在<http://www.mitk.org/wiki/Converting_a_BlueBerry_bundle_to_a_CTK_plugin>：

     写的东西和org.sv.gui.qt.pathplanning中的部分文件和代码很像，操作了一些类似于.cmake的文件，加入了sv4gui_PathPlanningPluginActivator里面的类似代码，在这个代码里有注册了一大堆pathplanning相关的action，说是要变成CTK插件，？？？

3. Developer Manual探索

   * <http://docs.mitk.org/2016.11/Introduction.html>

     ```
     As part of the viewer perspective, an instance of QmitkDataManagerView allows for data selection. Visualization of the selected data is then provided by a simple render window view. 
     ```

   * http://docs.mitk.org/2016.11/IntroductionSelectionService.html#BlueBerrySelectionServiceIntro_WhatCanBeSelected

     ```
     The selection service provided by the BlueBerry workbench allows efficient linking of different parts within the workbench window: View parts that provide additional information for particular objects and update their content automatically whenever such objects are selected somewhere in the workbench window. 
     ```

     这里有可能解释为什么类与类之间的数据传递。

   * <http://docs.mitk.org/2016.11/BlueBerryExampleSelectionServiceMitk.html>

     这里有讲data node selection，而且被选择的data node的值也被传到另一个view中。

4. 我发现类分的越细，越有层次和相互关联，越能体现面向对象的思维。

5. * sv4guiMainWindow类与sv4guiAppliaion类有关。sv4guiApplication::Application()可以返回一个单例。

   * view与view之间大部分通过Selection Service进行连接

6. **总结**：

   * 目标：了解SimVascular用QT和BlueBerry进行GUI开发的机制（workbench和QTwidget的关系）

     ​			了解里面数据流动的机制（包括Selection Service）

     ​			CTK插件的用法（activator和配置文件的作用）

   * 方法：继续看DataManager，sv4guiMainWindow，project create相关源码

     ​            安装并学习QT，MITK

     ​			还是要多看MITK的manual不管是user的还是developer的

     ​			CTK插件教程（什么叫ctkPluginActivator）

     ​            

# 2019/8/14

### SimVascular

*sv4gui_MainWindow*

```c++
//*****
//sv4guiMainWindow()
//*****
m_DataStorage= sv4guiApplication::application()->dataStorage();

svDisplayEditor* displayEditor=new svDisplayEditor;
m_DisplayWidget=displayEditor->GetDisplayWidget();
sv4guiApplication::application()->setDisplayWidget(m_DisplayWidget);

sv4guiQmitkDataManager* dataManager= new sv4guiQmitkDataManager;

sv4guiApplication::application()->setExtensionManager(new svExtensionManager(&xmlFile));
//和后面的mainwindow的UI布局有关。

QList<svExtensionManager::svViewInfo*> viewList=sv4guiApplication::application()->extensionManager()->getViewList();

QSignalMapper* signalMapper = new QSignalMapper(this);

//*****
//connectViews() 这个函数为什么只声明却没有定义？
//*****

/*
我似乎并没有在sv4guiMainWindow中找到关于File, Edit, Window等这些menu的设置代码。
但是我又在sv4gui_WorkbenchWindowAdvisor中找到蛛丝马迹，原来：
是在sv4gui_WorkbenchWindowAdvisor中写了那些比较常规的GUI控件，里面还写了IPartListener
*/
```

plugin.xml里面似乎有有用的东西。

*sv4gui_Main*

```cpp
int sv4guiMain(int argc, char *argv[],bool use_provisioning_file, bool use_workbench) {
    app.setProperty(ctkPluginFrameworkLauncher::PROP_PLUGINS, pluginsToStart);
     //Use transient start with declared activation policy
     ctkPlugin::StartOptions startOptions(ctkPlugin::START_TRANSIENT | ctkPlugin::START_ACTIVATION_POLICY);
     app.setProperty(ctkPluginFrameworkLauncher::PROP_PLUGINS_START_OPTIONS, static_cast<int>(startOptions));    
}
```

perspective我不太懂。

ctkPluginFrameworkLauncher是什么东西。

*sv4gui_WorkbenchWindowAdvisor*

```cpp
void sv4guiWorkbenchWindowAdvisor::PostWindowCreate()
{
    auto perspGroup = new QActionGroup(menuBar); //
    //...
    svViewActions.push_back((*svMapIter).second); //常见模块（如pathplanning）的actions的列表
}
```

gui里的image navigator要是被关闭掉了，发什么了什么？在按下menu bar中image navigator和这个view又出现了之前，发生了什么？这个和``sv4guiWorkbenchWindowAdvisorHack::undohack``以及它的函数``onImageNavigator()``又有什么关系？

view是怎么显示出来的，QAction的setChecked()又是怎么回事？

# 2019/8/15

## SimVascular

``sv4gui_WorkbenchAdvisor``里ImageNavigator是怎么显示出来的，imageNavigatorAction发出triggered()信号后，是怎么setChecked()的？

我也不知道CreateAndRunWorkbench函数执行了什么东西。

ActionBarAdvisor是干嘛的？

```sv4gui_WorkbenchWindowAdvisor```

```cpp
class PartListenerForImageNavigator: public berry::IPartListener
{
	void PartVisible(const berry::IWorkbenchPartReference::Pointer& ref) override
    {
        if (ref->GetId()=="org.mitk.views.viewnavigatorview")
        {
            imageNavigatorAction->setChecked(true);
        }
    }
    /*原来是通过IPartListener来监听imageNavigator这个view的变化，然后更新imageNavigatorAction的状态。通过以下方式注册：
    window->GetPartService()->AddPartListener(imageNavigatorPartListener.data());
    但是在这个文件中其他设置的listener我都看不懂要干嘛。
    */
}
```

display，data manager，image navigator那几个view是在什么时候打开的。

```sv4gui_WorkbenchWindowAdvisor```

```cpp
void sv4guiWorkbenchWindowAdvisor::SetupDataManagerDoubleClick()
{}

void sv4guiWorkbenchWindowAdvisor::ShowSVView()
{
}
/*
这里展示了从双击data manager的一个data node到某一个view打开的实现代码。
*/
```

原来是在```sv4gui_DefaultPerspective``里设置了那几个view。

pathplanning的很多action是如何加到path上的，还有rename，copy，paste这3个action是如何加到data manager的每个data node上的。

如何软件实现双击或单击data node。

在sv project未打开之前，data manager里面是什么，空的sv project打开之后，data manager里面是什么，添加了path之后底层做了什么，一开始就打开具有path的sv project之后，data manager里面是什么。

**``QmitkDataManagerView``文件**

```cpp
void sv4guiQmitkDataManagerView::RemoveSelectedNodes( bool ){
    //...
    mitk::DataNode::Pointer node = 0;
    question.append(QString::fromStdString(node->GetName()));//可得到data node的名字，如aorta
    this->GetDataStorage()->Remove(node);//访问所有的数据this->GetDataStorage()
    //...
}
void sv4guiQmitkDataManagerView::ShowOnlySelectedNodes( bool )
{
    QList<mitk::DataNode::Pointer> allNodes = m_NodeTreeModel->GetNodeSet();

  foreach(mitk::DataNode::Pointer node, allNodes) //通过这种方式可以访问到data node
  {
    node->SetVisibility(selectedNodes.contains(node));
  }
}
void sv4guiQmitkDataManagerView::NodeTableViewContextMenuRequested( const QPoint & pos )//Shows a node context menu.
{
    actions = QmitkNodeDescriptorManager::GetInstance()->GetActions(node);//得到一个data node的所有context action（如Import Paths），然后可以通过QAction::text()函数（返回"Save","Reinit"之类的东西）区分不同的action。
}
```

我怀疑sv4guiQmitkDataManagerView::GetDataStorage()存储的是image,path,model之类的信息，而且这些信息可直接用于RenderingManager画render window。

data node的类型(image, path, mesh等)是在哪里指定的，如何避免在同一个folder下（同一个folder下data node的类型相同）名字相同。

如何用软件方式向槽函数传递参数（手动触发自动就传了，但软件触发就不知道了）

``sv4gui_QmitkDataManagerView``

```cpp
//为了可以传递参数，因此要自行调用槽函数
void sv4guiQmitkDataManagerView::ContextMenuActionTriggered( bool )
{
  QAction* action = qobject_cast<QAction*> ( sender() );

  std::map<QAction*, berry::IConfigurationElement::Pointer>::iterator it
    = m_ConfElements.find( action );
  if( it == m_ConfElements.end() )
  {
    MITK_WARN << "associated conf element for action " << action->text().toStdString() << " not found";
    return;
  }
  berry::IConfigurationElement::Pointer confElem = it->second;
  svmitk::IContextMenuAction* contextMenuAction = confElem->CreateExecutableExtension<svmitk::IContextMenuAction>("class");

  QString className = confElem->GetAttribute("class");

  contextMenuAction->SetDataStorage(this->GetDataStorage());
  contextMenuAction->SetFunctionality(this);

  if(className == "QmitkCreatePolygonModelAction")
  {
    QString smoothed = confElem->GetAttribute("smoothed");
    if(smoothed == "false")
    {
      contextMenuAction->SetSmoothed(false);
    }
    else
    {
      contextMenuAction->SetSmoothed(true);
    }
    contextMenuAction->SetDecimated(m_SurfaceDecimation);
  }

  contextMenuAction->Run( this->GetCurrentSelection() ); // run the action
}
```



### 如何更改PostWindowOpen()函数

1. 软件触发open sv project的操作（需要提前更改相关的action，可设置sv project路径为固定的，不和用户交互）
2. 软件触发import path的操作（需要提前更改相关的action，设置.paths文件为固定路径的，不和用户交互）
3. 软件触发创建一个contour group，软件触发打开sv 2d segmentation的gui，然后软件设置要画contour的path point，软件触发画contour（要向槽函数传递两个参数，中心点和半径，更改相关函数）。完成所有的画contour的操作，要两层循环。

# 2019/8/16

## SimVascular

### 研究过程中收集的资料

```cpp
page->ShowView("org.sv.views.pathplanning");
```

明明有很多个path，但这里要打开path editor，怎么知道要用哪个path的数据呢？不管怎么样，我可以想办法触发doubleClicked（或直接调用展示view的函数）和selection data node。

``sv4gui_WorkbenchWindowAdvisor``文件

```cpp
	berry::IViewPart::Pointer dataManagerView = window->GetActivePage()->FindView("org.sv.views.datamanager");
    if(dataManagerView.IsNull())
        return;

    sv4guiQmitkDataManagerView* dataManager=dynamic_cast<sv4guiQmitkDataManagerView*>(dataManagerView.GetPointer());
    QTreeView* treeView=dataManager->GetTreeView();

    QObject::connect(treeView, SIGNAL(doubleClicked(const QModelIndex &)), this, SLOT(ShowSVView()));
/*源码中将doubleClicked信号和ShowSVView槽联系在一起。
*/
```

原来sv4guiSeg2DEdit类和其他模块的editor（比如操作pathplanning的编辑器）提前注册在plugin.xml文件。

在sv 2D segmentation的view中，点击"Circle"，实际上发出了QPushButton的clicked()信号。

我直接调用sv4gui_Seg2DEdit的OnSelectionChanged()函数来模拟单击contour group node行吗？

``sv4gui_Seg2DEdit``

```cpp
connect(ui->resliceSlider,SIGNAL(reslicePositionChanged(int)), this, SLOT(UpdatePathPoint(int)) );
//更改resliceSlider
```

``sv4gui_Seg2DEdit``

```cpp
void sv4guiSeg2DEdit::CreateManualCircle(bool)
{
}
//更改这个函数，传入参数,x,y,r就可以完成画contour的操作。
```

如何模拟选择data node？？？？

**原来MITK中有selection service，以及实现这个的各种类的系统，表现为：selection改变，则会执行回调函数OnSelectionChanged()函数**

### 具体过程

由于QAction只有一个triggered()，由于我的需要，我要重载几个triggered()函数。第一个是：triggered(const QList\<mitk::DataNode::Pointer\> &selectedNodes) ，用于Import Paths; 第二个是triggered(mitk::DataNode::Pointer path, double r)，用于画contour。...... 这种方法不行，因为子类的指针转换到基类的指针会出毛病，定义QAction的子类来写重载函数这种方法在SimVascular行不通。

### 修改的SimVascular

* sv4gui_WorkbenchWindowAdvisor.cxx

  1080行

* sv4gui_QmitkDataManagerView.h

  63行，302行

* sv4gui_QmitkDataManagerView.cxx

  517行, 1122行

* sv4gui_PathLoadAction.cxx

  86行

# 2019/8/17

## SimVascular

### 研究过程中收集的资料

* 几种访问selected data node的方法：

  ```QmitkDataManagerView``` 

  ```cpp
  //方法1
  void sv4guiQmitkDataManagerView::RemoveSelectedNodes( bool ){
    QModelIndexList indexesOfSelectedRowsFiltered = m_NodeTreeView->selectionModel()->selectedRows();
    QModelIndexList indexesOfSelectedRows;
    for (int i = 0; i < indexesOfSelectedRowsFiltered.size(); ++i)
    {
      indexesOfSelectedRows.push_back(m_FilterModel->mapToSource(indexesOfSelectedRowsFiltered[i]));
    }
    for (QModelIndexList::iterator it = indexesOfSelectedRows.begin()
      ; it != indexesOfSelectedRows.end(); it++)
    {
      node = m_NodeTreeModel->GetNode(*it);
    }
  }
  //方法2
  void sv4guiQmitkDataManagerView::OpacityChanged(int value){
      mitk::DataNode* node = m_NodeTreeModel->GetNode(m_FilterModel->mapToSource(m_NodeTreeView->selectionModel()->currentIndex()));
  }
  ```

  ```sv4gui_WorkbenchWindowAdvisor```

  ```cpp
  //方法3
  std::list< mitk::DataNode::Pointer > sv4guiWorkbenchWindowAdvisor::GetSelectedDataNodes()
  {
      berry::ISelectionService* selectionService =window->GetSelectionService();
      mitk::DataNodeSelection::ConstPointer nodeSelection = selectionService->GetSelection().Cast<const mitk::DataNodeSelection>();
      return nodeSelection->GetSelectedDataNodes();
  }
  ```

* 我可以直接通过``page->ShowView("org.sv.views.segmentation2d");``这种方式展示view；我可以通过直接传入参数调用sv4gui_2DEdit的OnSelectionChanged()函数以及发送信号``reslicePositionChanged(int))``；我可以通过``QList<mitk::DataNode::Pointer> allNodes = m_NodeTreeModel->GetNodeSet();``访问所有data node，并找到action；我可以通过修改``void sv4guiSeg2DEdit::CreateManualCircle(bool)``来画contour；剩下的就是访问``pubVesselRadius.txt``文件来得到半径。我还要知道path的data node中的数据是以什么方式存储。

  还有，为了创建contour group，要重载``sv4gui_ContourGroupCreateAction``里的run函数。在此之前，看看create contour group的实现。.......事实证明：还是不要给源码中的virtual函数重载。

* ``sv4guiPath* selectedPath=dynamic_cast<sv4guiPath*>(selectedPathNode->GetData());``通过这种方式，将data node转为sv4guiPath类型；

  ```sv4gui_PathLegacyIO```

  ```cpp
  sv4guiPathElement* pe = new sv4guiPathElement();
  path->SetPathElement(pe);
  //原来在从文件中读入path时，一个sv4guiPath保存一个sv4guiPathElement,只要操作sv4guiPathElement。
  ```

  ``sv3_PathElement``

  ```cpp
  PathElement::PathPoint PathElement::GetPathPoint(int index){}
  //得到一个path point
  
  std::array<double,3> PathElement::GetPathPosPoint(int index){}
  //得到一个path point的3维坐标点。
  
  int PathElement::GetPathPointNumber(){}
  //得到所有path point的个数
  ```

  ``sv4gui_Seg2DEdit``

  ```cpp
  void sv4guiSeg2DEdit::ManualCircleContextMenuRequested(const QPoint &pos) //slot
  {
      connect(&action1, SIGNAL(triggered()), this, SLOT(CreateManualCircle()));
  }
  //看来必须在这里connect的时候，顺便emit,执行create circle操作
  
  void sv4guiSeg2DEdit::CreateQtPartControl( QWidget *parent )
  {
      connect( ui->btnCircle,SIGNAL(customContextMenuRequested(const QPoint&)), this, SLOT(ManualCircleContextMenuRequested(const QPoint&)) );
  }
  //要找到ui
  ```

  

### 问题

1. mitk::DataNode::Pointer node; 怎么让node为空

   sv4guiQmitkDataManagerView::GetPathNodeByName(string name)

2. mitk::DataNode::Pointer与mitk::DataNode*之间的转换是什么？

   sv4gui_WorkbenchWindowAdvisor 的1112行

# 2019/8/18

## SimVascular

### 研究过程中收集的资料

```sv4gui_ModelEdit```

```cpp
//modeling过程
connect(ui->btnUpdateModel, SIGNAL(clicked()), this, SLOT(ShowSegSelectionWidget()) );//-->
connect(m_SegSelectionWidget,SIGNAL(accepted()), this, SLOT(CreateModel()));//-->
void sv4guiModelEdit::CreateModel(){}; //-->
m_SegSelectionWidget; //-->
QAction* useAllAction=m_NodeMenu->addAction("Use All");
QObject::connect( useAllAction, SIGNAL( triggered(bool) ) , this, SLOT( UseAll(bool) ) );//-->

```

### 问题

我按照我认为的对的操作实验以下，找3个段的血管（1个父亲，两个儿子）用manual create circle方法segmentation，但是画的模型跟我预想的不一样，我怀疑可能是以下方面出问题了：

* 对manually create circle中的参数x,y的理解有问题
* .paths文件中的rotation项有问题

# 2019/8/26

## 看论文

### The HAM10000 Dataset: A Large Collection of Multi-Source Dermatoscopic Images of Common Pigmented Skin Lesions

1. 摘要部分（Abstract）：

   动机（被迫于什么原因），

   做了什么事，

   具体怎么做的（``Given this diversity we had to apply diﬀerent acquisition and cleaning methods and developed semiautomatic workﬂows utilizing speciﬁcally trained neural networks.``），

   最后的结果是什么样，

   这个结果有什么实际用处。

2. Background & Summary

   过去在`` small sample size``的情况下训练神经网络的局限性以及现在硬件的发展

   具体解释了自己的数据集在当前环境下发行的必要性（训练神经网络要大量图片，过去的数据集有种种不足，还有自己的数据集发行在很好的网站）

   具体解释了做``a classifier for multiple diseases `` 的必要性（二分类不能满足现实中复杂的疾病种类的检测需要）

3. Methods

   具体解释了由raw images到规范的数据集所用到的方法

4. Data Records 

   解释了了methods，还要具体解释用这些methods处理的图片或数据的数据源。

## 如何写大创论文

我觉得写从相机拍摄视频到最终预测轨迹的整个过程：相机矫正，测距以及预测

# 2019/8/27

## SimVascular

### 若编译成功，需要提前放好什么文件

1. ~/SVProject
2. ~/SVProject/Paths/0.paths
3. ~/SVProject/Paths/radius.txt

# 2019/8/29

## SimVascular

**目前，放弃通过修改源码来自动化得到模型文件的方法，转用pthon接口**

## SimVascular-Tests

<https://github.com/SimVascular/SimVascular-Tests/blob/master/python_demos/PathDemo.py>

### PathDemo.py

```python
#import to visualize in the repository
GUI.ImportPathFromRepos('path1')
GUI.ImportPathFromRepos('path1','Paths')
#在软件的python控制台下运行以上代码会提示error并退出整个程序
```

# 2019/8/30

## SimVascular-Tests

**我今天才发现**：

1. SV的坐标系设置与我在做眼球血管遍历时用的坐标系不一致，需要坐标变换，变到SV的坐标系

   (x0, y0, z0) ---> (x0, z0, height-y0) (其中，z0必须是深度，且深度越深，值越小，height是血管照片的高度)

2.  在SV的manually create circle按钮实现的功能中，输入的x,y是在血管切线法平面上相对于中心点（path point）的偏移值

**总结**

1. 使用python接口发现不知道怎么loft，官方教程很不详细，我只能找python接口的源码以及loft的源码来看。

2. 用软件来试着生成一个有3个path的model时，无法生成（两个儿子的第一个point是父亲的最后一个point），这个问题可能直接导致自动化生成眼球血管的model（眼球血管很复杂）的失败。但是create model时用``OpenCASCADE``的Model Type会生成。

3. loft有讲究，若直接loft，即使成功生成model，父亲和儿子的交点处会有问题（官网教程又讲）

   <http://simvascular.github.io/docsModelGuide.html#modelingImportingExportingPaths>

4. 若直接修改源码，除了不能找到view对象之外，还要更改一个地方：不能直接import .paths文件，而要通过``add manully``的方式，这样会更好，而且SV坐标系和我的坐标系不同。

**2019/8/16的日记有重要信息**

