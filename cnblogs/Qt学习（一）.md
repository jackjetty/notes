#1. 简介
* 跨平台 GUI 通常有三种实现策略
    * API 映射
        * 相当于将不同平台的 API 提取公共部分。界面库使用同一套 API，将其映射到不同的底层平台上面。**相当于取交集**
        * 如wxWidgets。
        * 优点：工作量小，用的是各平台的原生组件，行为一致
        * 缺点：只有公共控件，编写库代码的时候需要大量工作用于适配不同平台
    * API 模拟
        * 在API映射的基础上，不同平台的有差异 API，将使用工具库自己的代码用于模拟出来。**相当于取并集**
        * 如wine和微软的DirectorX。
        * 优点：应用程序无需重新编译，即可运行到特定平台上
        * 缺点：
    * GUI 模拟
        * 相当于用各平台的底层图形绘制函数等进行重绘
        * 如 gtk+、swing、**Qt**。
        * 优点：可以方便得修改组件外观
        * 缺点：工作量大
* QLabel label("Hello, world");比直接new要好，避免了内存泄露

#2. 信号槽机制
* 所谓信号槽，实际就是观察者模式。当某个事件发生之后，比如，按钮检测到自己被点击了一下，它就会发出一个信号（signal）。这种发出是没有目的的，类似广播。如果有对象对这个信号感兴趣，它就会使用连接（connect）函数，意思是，用自己的一个函数（成为槽（slot））来处理这个信号。也就是说，当信号发出时，被连接的槽函数会自动被回调。这就类似观察者模式：当发生了感兴趣的事件，某一个操作就会被自动触发。
* QObject
    * 一个类想要有信号槽功能，必须继承QObject，并且在类定义中使用**Q_OBJECT宏**，其在宏展开时会为类提供信号槽机制、国际化机制以及 Qt 提供的不基于 C++ RTTI 的反射能力
    * Q_OBJECT这个宏会由moc进行处理。这里你可以将moc其理解为一种预处理器，是比 C++ 预处理器更早执行的预处理器。
        * moc 会读取标记了 Q_OBJECT 的头文件，生成以 moc_ 为前缀的文件，比如 newspaper.h 将生成 moc_newspaper.cpp。你可以到构建目录查看这个文件，看看到底增加了什么内容。注意，由于 **moc 只处理头文件中的标记了Q_OBJECT的类声明**，不会处理 cpp 文件中的类似声明。
    * 发送者和接收者都需要是QObject的子类（当然，槽函数是全局函数、Lambda 表达式等无需接收者的时候除外）
* connect
    * 使用connect的多个重载函数，来定义和建立信号槽关系（谁触发，谁接受，传什么，接受者干什么）
    * 使用QObject::connect()函数连接信号和槽。
* signals
    * 使用 signals 标记信号函数，信号是一个函数声明，返回 void，不需要实现函数代码；
    * 类中的signals块所列出的，就是该类的信号。信号就是一个个的函数名，返回值是 void（因为无法获得信号的返回值，所以也就无需返回任何值），参数是该类需要让外界知道的数据。信号作为函数名，不需要在 cpp 函数中添加任何实现
* SIGNAL和SLOT宏
    * 多用于connect函数中，将函数转换为字符串参数
    * 多用于Qt4？？，因此其只能有字符串参数？
* emit
    * 是 Qt 对 C++ 的扩展，是一个关键字（其实也是一个宏）。emit 的含义是发出，也就是发出某个信号
    * 使用 emit 在恰当的位置发送信号；
* 槽函数是普通的成员函数，作为成员函数，会受到 public、private、protected 的影响；

#3. 模块
* Qt 5 与 Qt 4 最大的一个区别之一是底层架构有了修改。Qt 5 引入了模块化的概念，将众多功能细分到几个模块之中。Qt 4 也有模块的概念，但是是一种很粗的划分，而 Qt 5 则更加细化。
* Qt 基础模块主要有：Qt Core、Qt Gui、Qt Multimedia、Qt Network、Qt Qml、Qt Quick、Qt SQL、Qt Test、Qt Webkit。
* 比如使用vs+插件创建Qt工程时，有一步会让你选择模块，默认选中了Qt Core、Qt Gui、Qt Webkit。

#4. 界面
* 比如使用vs+插件创建Qt工程时，有一步会让你设置要创建的默认界面，基类可以选择QMainWindow、QWidget或者QDialog，同时生产.h、.cpp、.ui、.qrc文件。

#5. 其他
* IDE可以使用
    * visual studio 2012 + Qt + qt-vs-addin
    * visual studio 2017 + Qt + qt-vsaddin-msvc2017-2.2.1.vsix
    * QCreator
* vs中可以先用Qt Designer做界面（类似winform），生成.ui文件、ui_xxx.h及moc_xxx.cpp文件，然后在自己的xxx.cpp中:QDialog(parent), ui(new Ui::DialogPasswordChange)