* vs使用
    * **C++项目中，Solution Explorer主要是逻辑视图（不对应文件的逻辑位置，没有文件夹只有Filter的概念。头文件源文件被自动分配到Header Files和Source Files下面）；不像C#那样主要是物理视图，文件夹和文件通常对应物理位置**
    * 工程目录的.cpp和.h文件通常在Include到工程后，会自动刷新到Solution Explorer中，然后可以自行放到想放的filter中
    *  如果.h文件在当前目录下，但是没有加入到工程中，此时在程序中又要包含该.h文件，那么，编译后，在工程中便产生了相应的External Dependencies文件夹，其中有该.h文件，也就是说成功把该.h文件添加到了工程中了？？
* build过程
    * 先编译，会用到Properties->Configuration Properties->C/C++中的配置
        * General->Additional Include Directories（头文件目录）
        * 一般vs能直接识别的函数调用错误等，都是缺少头文件导致的
    * 再链接，会用到Properties->Configuration Properties->Linker中的配置
        * General->Additional Library Directories（lib文件目录）
        * General->Input（lib文件名）
        * 即使使用的是动态库，也需要指定这些，因为不像C#的动态库那样自包含元数据，C++在使用动态库dll时还需要.h头文件和.lib文件
        * 一般build过程中，到了链接步骤才报的错，通常是链接时没有找到lib文件导致的
* 内存使用
    * 关掉程序或者杀掉进程时，使用new分配的内存都会被回收
    * 程序中如果访问、使用了非法地址的内存（其他应用程序的内存），处于windows的保护机制，应用程序直接会挂掉
    * 对于new分配的内存，如果没有使用delete释放掉（或者通过Qt的对象树机制自动回收），那么也只是在程序运行期间可能会导致内存泄露，内存越用越多，在程序关闭或被杀掉进程后，也不会再占用内存。
* MFC和Qt对比
    * [参考](https://blog.csdn.net/changyourmind/article/details/51778555)
* 类定义
    * 通常把类的声明、成员函数的声明放在头文件中，在源文件中通过::运算符分别定义各个成员函数
    * 可以在源文件中分别定义各个成员函数，不像C#和Java那样要用大括号把所有的东西包起来（当然可以用反射的方式添加函数，C#也支持partial class）