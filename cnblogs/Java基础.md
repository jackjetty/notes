* 如果是用命令来运行java程序（如Java Application版的HelloWorld）
    * 先用javac xxx.java来编译源文件生成xxx.class中间字节码，然后用java xxx命令来运行
    * 在java命令中可能需要使用-cp参数来指定class文件目录（否则就要将classpath配在环境变量中了），后面跟着的xxx名字前面要加上报名。并且目录结构要和包结构一致！！！
    * IDE、Spring Boot等框架中，已经帮忙处理好了build path的问题，比如在project的build path中？
* java程序在根据包名、类名查找类时，靠的是classpath的设置，其指定了源码的根目录，也就是根包所在位置，所以在用命令运行java程序时才需要指定classpath或文件夹路径。而IDE或者Maven、Gradle等项目，会帮你处理这些事情，不再需要手动设置classpath（应该是在Run as或者Debug as的Configuration中的classpath中配置的，默认帮你把当前目录加上了）。
* 当机器上有多个jdk、jre时，Path环境变量中，谁在前面谁优先。
* Eclipse、开发过程需要jdk，而不是jre，IDE可以在设置里指定用哪个，但最好还是在环境变量里，把jdk的目录放在前面，避免其他程序和操作系统带的java优先导致错误。