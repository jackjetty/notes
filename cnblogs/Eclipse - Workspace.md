* 概念
    * 每个Workspace表现为一个文件夹，默认会有.metadata、RemoteSystemsTempFiles和Servers两个文件夹
    * 每个Workspace共享一些Settings，如Workbench Layout、Working Sets、Preferences设置（还是Eclipse全局的？另外貌似修改之后就会影响当前workspace的所有project，如果project单独勾选覆盖preferences？）等，在切换workspace时可以选择Copy某些Settings。
    * 安装Eclipse或者STS后，默认就会有一个Workspace（比如sts），就是第一次打开时选择的目录。
    * 每个Workspace中可以包含多个project、working set。
* 用途
    * 有时一个项目会有多个eclipse project，而两组项目中可能会有重名的eclipse project，在同一个workspace中可能会有问题
    * 为了区分不同的Preferences设置
    * 为了区分不同的Workbench Layout个性设置（可能是为了适应不同的项目类型引起的不同风格）
    * 如果只是简单得想把一些eclipse project分个组，也许同一workspace下的不同working set就可以了
* 其他
    * 每次打开eclipse/sts时，会有弹窗让你选择workspace，可以勾选让它下次不再弹出，也可以在Windows->Preferences->General->Startup and Shutdown->Workspaces中重新选中让它在下次启动时再次弹出。
    * 如果所有的workspace都在D:/workspace/sts目录下，那么可以在其中创建default、projectname等多个workspace，让所有的workspace都在这个目录下，容易管理。
    * import到workspace中的project，其实代码还是在原位置，workspace中只是有引用和metadata
    * workspace和项目代码不能在同一目录，但是可以项目代码是workspace文件夹的子目录