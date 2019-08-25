#1. 概述
* Qt做UI设计后，通常会生成Form Files文件夹下的ui文件、Generated File文件夹下的ui_xxx等头文件和源文件、Header Files文件夹下的头文件、Source Files文件夹下的源文件

#2. 
* **打开一个新的Qt Dialog可以用exec方法**，比如按钮点击触发或菜单按钮点击触发
    * 模态？关闭后回到代码继续进行
* moc
    * Qt 将源代码交给标准 C++ 编译器，如 gcc 之前，需要事先将这些扩展的语法去除掉。完成这一操作的就是 moc。
    * moc 全称是 Meta-Object Compiler，也就是“元对象编译器”。Qt 程序在交由标准编译器编译之前，先要使用 moc 分析 C++ 源文件。如果它发现在一个头文件中包含了宏 Q_OBJECT，则会生成另外一个 C++ 源文件。这个源文件中包含了 Q_OBJECT 宏的实现代码。这个新的文件名字将会是原文件名前面加上 moc_ 构成。这个新的文件同样将进入编译系统，最终被链接到二进制代码中去。因此我们可以知道，这个新的文件不是“替换”掉旧的文件，而是与原文件一起参与编译。另外，我们还可以看出一点，moc 的执行是在预处理器之前。因为预处理器执行之后，Q_OBJECT 宏就不存在了。
    * 在命令行下输入moc yourfilename.h -o moc_youfilename.cpp生成不带Q_OBJENT的源文件。

#3. 开发流程
* 使用QT5->Launch Designer打开设计工具，设计界面，生成ui文件
* 根据ui文件生成ui_xxx.h头文件
    * 手动
    * QCreator自动？
    * vs+Qt5自动
        * vs+Qt5+addin创建的工程自动？
        * 非vs+Qt5+addin创建的工程：
            * 在.ui文件上右击打开属性窗口，选择常规-项类型，选择自定义生成工具，Apply后会自动重新打开（或者保存后自己再打开），就出现了自定义生成工具选项
            * Command Line配置成"$(QTDIR)\bin\uic.exe" -o "$(ProjectDir)\GeneratedFiles\ui_%(Filename).h" "%(FullPath)"，文件全路径名中除了%(Filename)参数都可以自己定
            * Outputs配置成$(ProjectDir)\GeneratedFiles\ui_%(Filename).h;%(Outputs)，文件全路径名中除了%(Filename)参数都可以自己定
* 根据ui_xxx.h头文件生成moc_xxx.cpp源文件
    * 手动
        * 切换到Qt的bin目录（如D:\Qt\Qt5.5.1\5.5\msvc2012\bin），然后uic TestQtDialog.ui -o TestQtDialog.h
    * QCreator自动？
    * vs+Qt5自动
        * vs+Qt5+addin创建的工程自动？
        * 非vs+Qt5+addin创建的工程：
            * 在ui_xxx.h文件上右击打开属性窗口，选择常规-项类型，选择自定义生成工具，Apply后会自动重新打开（或者保存后自己再打开），就出现了自定义生成工具选项
            * Command Line配置成"$(QTDIR)\bin\moc.exe"  "%(FullPath)" -o ".\GeneratedFiles\$(ConfigurationName)\moc_%(Filename).cpp"，文件全路径名中除了$(ConfigurationName)和%(Filename)参数都可以自己定
            * Outputs配置成.\GeneratedFiles\$(ConfigurationName)\moc_%(Filename).cpp，文件全路径名中除了$(ConfigurationName)和%(Filename)参数都可以自己定
* 工程包含ui_xxx.h和moc_xxx.cpp所在目录
            * Additional Dependencies设置成$(QTDIR)\bin\moc.exe;%(FullPath)
    * 通常还是会把ui文件、ui_xxx.h、moc_xxx.cpp文件include放到工程中，并且放在特定的物理文件夹和filter里

#5. 其他
* 链接时报错"No relevant classes found. No output generated."
    * 通常是因为该头文件不需要生成moc文件，也就是不需要moc'ing
    * 选中该头文件，Properties中把Item Type从Qt Moc Tool改成C/C++ Header就好了
    * **只有包含Q_OBJECT的.h头文件才需要被moc'ing**
    * [参考](http://www.cnblogs.com/piaoger/archive/2012/01/21/2328574.html)
* 链接时报错"unresolved external symbol "public: static struct QMetaObject const QWinWidget::staticMetaObject" (?staticMetaObject@QWinWidget@@2UQMetaObject@@B)"等
    * [参考](https://stackoverflow.com/questions/14170770/unresolved-external-symbol-public-virtual-struct-qmetaobject-const-thiscal)