```cpp
// find contextMenuAction extension points and add them to the node descriptor
  berry::IExtensionRegistry* extensionPointService = berry::Platform::GetExtensionRegistry();
  QList<berry::IConfigurationElement::Pointer> cmActions(extensionPointService->GetConfigurationElementsFor("org.sv.gui.qt.datamanager.contextMenuActions") );
```

以上代码可以加载插件（不准确的说），效果是为很多data node加载它们的action，插件信息提前写在plugin.xml文件中，有很多插件，也就有很多plugin.xml，在主程序运行时，会调用ctkPluginActivator，通过这个activator来注册插件。下面展示plugin.xml。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<plugin>

  <extension point="org.blueberry.ui.views">
    <view id="org.sv.views.pathplanning"
          name="SV Path Planning"
          class="sv4guiPathEdit"
          icon="resources/pathedit.png" />
  </extension>

  <extension point="org.sv.gui.qt.datamanager.contextMenuActions">
      <contextMenuAction nodeDescriptorName="sv4guiPathFolder" label="Create Path" icon="" class="sv4guiPathCreateAction" />
      <contextMenuAction nodeDescriptorName="sv4guiPathFolder" label="Import Paths" icon="" class="sv4guiPathLoadAction" />
      <contextMenuAction nodeDescriptorName="sv4guiPathFolder" label="Export All as Legacy Paths" icon="" class="sv4guiPathLegacySaveAction" />
      <contextMenuAction nodeDescriptorName="sv4guiPath" label="Point 2D Size" icon="" class="sv4guiPathPoint2DSizeAction" />
      <contextMenuAction nodeDescriptorName="sv4guiPath" label="Point 3D Size" icon="" class="sv4guiPathPoint3DSizeAction" />
  </extension>

</plugin>
```

