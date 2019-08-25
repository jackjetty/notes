#1. Activity Bar
##1.1 Explorer
###1.1.1. OPEN EDITORS
所有在右侧编辑区打开的文件列表
###1.1.2 {PROJECTNAME}
某个文件夹下的文件树
##1.2 Search
##1.3 Source Control
##1.4 Debug
比如在安装了C++插件后，既可以从这里的绿色开始图标按钮，开始调试。当然由于VS Code只是个编辑器，所以要装好编译器并修改好launch.json文件。
##1.5 Extensions(: MARKETPLACE)
###1.5.1 EXTENSIONS
####1.5.1.1 在线安装
直接在EXTENSIONS下的搜索框中输入搜索，搜索结果可以排序，找到后点击安装，一般需要Reload。
####1.5.1.2 离线安装
下载vsix格式的VS Code扩展安装包，如[C++插件](https://github.com/Microsoft/vscode-cpptools/releases)。
然后从View->Command Palette或快捷键Ctrl+Shift+P打开命令输入框，输入"Install From VSIX"，回车后选择安装包，即直接开始安装，完成后右下角提示Reload。
###1.5.2 ENABLED
###1.5.3 RECOMMENDED