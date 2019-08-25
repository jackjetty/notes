* EclEmma
    * [官网 - Java Code Coverage for Eclipse](https://www.eclemma.org/installation.html#manual)
    * [测试覆盖率工具：EclEmma](https://www.cnblogs.com/Ming8006/p/5811425.html)
    * 实现原理
        * Instrumentation：代码注入，在产品代码的关键位置插入统计代码,从而获得测试覆盖率数据
            * **Class Instrumentation: 把统计代码插入编译好的.class文件**
            * Source Instrumentation：把统计代码插入源代码并编译成新的.class文件
        * Custom JVM：在JVM中把统计代码插入.class。测试覆盖率分析可以在JVM执行测试代码的过程中完成
    * 功能
        * 可以结合Junit或TestNG使用
        * 作为Eclipse/STS的插件来使用
        * 可以显示各层级的覆盖率情况
        * 可以在源码中高亮代码覆盖情况
        * 可以生成覆盖率测试报告
        * **既可以通过单元测试来进行覆盖率测试，也可以以Java Application等正常应用的形式来进行（编译出来或者跑起来以后，交给测试人员进行测试，测试完成之后再生产报告）。**
    * 覆盖率指标
        * 行覆盖率：  部分行覆盖：a>b?a:b
        * 基本语句块覆盖率：  基本语句块：没有if-else分支的代码区域
        * 类覆盖率：  由JVM加载并初始化
        * 方法覆盖率：  第一个基本语句块被执行
    * 安装
        * 离线安装。下载后解压出feature和plugin目录，并分别复制到Eclipse/STS的相应目录下，重启Eclipse/STS，工具栏中在调试、运行按钮后面会多出一个按钮，那么就是安装成功了
    * 使用
        * 选择想要进行覆盖率测试的Project
        * 然后在工具栏中按钮的下拉菜单中可以选择"Coverage As"后，可以进一步选择当做Java Application、Junit Test还是TestNG来进行覆盖率测试
        * 运行完成后，可以在"Coverage" View窗口中来查看覆盖率测试结果（各层级的覆盖率百分比）。同时该项目的代码中会用红/绿色高亮跑过/未跑过的代码。
        * 窗口中可以导入/导出Session结果为HTML等格式的文件报告