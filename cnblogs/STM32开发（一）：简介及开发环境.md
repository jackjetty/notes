#1. 背景
* STM32是意法（ST）公司开发的基于ARM Cortex-M系列的一系列微控制器（MCU）。
* 有两种库
    * 标准外设库（StdPeriph_Driver、Standard Peripheral library）：是开发STM32比较经典的库，直接反应底层（寄存器）的代码，适合初学者，或需要深入理解的人。
    * HAL库（STM32Cube HAL）：是由ST公司推出的一个较标准库更高层次抽象的库，多用于STM32CubeMX生成的代码，有点类似面向对象的库，里面封装了很多东西，对于想理解深入或底层的人， 不适合用这个库。
* 固件驱动等
    * [官网固件/驱动库](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software.html?querycriteria=productId=SC961)
    * [MCU固件包](https://my.st.com/content/my_st_com/zh/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-mcu-packages.html?querycriteria=productId=LN1897)，[F4系列固件包](https://my.st.com/content/my_st_com/zh/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-mcu-packages/stm32cubef4.html#getsoftware-scroll)，STM32CubeMX或者Keil会用到。
    * 包括Low level drivers, hardware abstraction layers, and middleware like RTOS, USB stacks, graphic stacks
    * 下下来解压后，包括_htmresc、Utilities、Project、Libraries目录和一些文件，KEIL的工程目录通常和这些类似。
* 软件开发工具
    * [官方列表](https://www.st.com/en/development-tools/stm32-software-development-tools.html?querycriteria=productId=SC2106)

#2. 开发环境及工具
* IDE
    * IAR
    * KEIL
        * [官网](http://www.keil.com/download/product)
        * 也就是uVision，是Keil公司出品的，属于所谓的MDK - Microcontroller Development Kit。
        * 不仅支持STM32等ARM Cortext-M系列MCU单片机的开发，还支持51等其他单片机，但要选择不同的版本下载，STM32对应MDK-ARM。
        * 分为MDK-Lite、MDK-Essential、MDK-Plus、MDK-Professional等级别，**不使用秘钥生成器破解的话，直接下载的是第一个Lite版本，代码大小限制只有32K**。
        * 使用
            * [STM32开发环境搭建](https://blog.csdn.net/u014630823/article/details/78509602)
            * [Keil的使用方法（汇总）](https://www.cnblogs.com/strongerHuang/p/5839141.html)
            * 官网下载安装包（ARM版）时需要注册
            * 安装完成后，如果要开发的芯片MDK不支持，那么需要通过弹出的Pack Installer去安装相应的包（也可以去[官网](http://www.keil.com/dd2/Pack/)下载），也可以先忽略关掉（后面可以再到Project->Options for Target xxx->Debug下面配置，或者File->Device Database），如果没有安装，会报错：device not included in legacy device database。
            * 选择芯片后，需要在创建固定的工程结构（在Manage Project Items弹窗中手动创建）
                * 自己的代码目录（名称可变）可以叫做App、User等
                * MDK-ARM或者Startup Code（名称可变）下面是startup_stm32xxxx_xd.s等启动文件（初始化堆栈、程序计数器、设置向量表异常事件的入口地址、调用main函数），结尾的ld、md、hd分别对应小、中、大容量产品。
                * CMSIS
                * xxx_StdPeriph_Driver
                * 还可以有FreeRTOS
                * 还可以有HARDWARE
            * 配置输出时创建Hex（在Project->Options for Target xxx->Output下面）
            * 配置下载/调试（在Project->Options for Target xxx->Debug下面）
            * 配置C/C++（在Project->Options for Target xxx->C/C++下面）
            * 配置包含路径（链接用，在Project->Options for Target xxx->C/C++下面）
            * 配置复位并运行设置（链接用，在Project->Options for Target xxx->Utilities下面）
    * **SW4STM32 + STM32CubeMX**
        * [参考-使用STM32CubeMX和SW4STM32在Nucleo-F401RE开发板轻松实现Blinky闪烁](https://www.yiboard.com/thread-781-1-1.html)
        * STM32CubeMX
            * [STM32CubeMX官方界面](https://www.st.com/content/st_com/zh/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-configurators-and-code-generators/stm32cubemx.html)
            * 简介
                * 是意法（ST）公司官方提供的初始代码生成器。
                * 图形化的
                * 便于配置STM32 microcontrollers
                * 能一步步生成C代码
                * **生成的代码有固定结构，限制较大，开发时也可以不用这个工具**
                * STM32Cube包含STM32CubeMX，除此之外还有许多example
            * 使用（创建Project和生成代码）
                * 配置网络
                    * 一般如果没有设置网络连接，那么打开时就会提醒进行配置（也可以后面生成代码的时候再配置），用于下载固件等库。如果用了公司代理，那么选择**手动配置代理**，使用系统代理没有效果可能是因为公司用的是pac脚本设置的代理呢，系统并没有指定代理的ip。然后填入代理信息，ip（浏览器有时会弹出让填账号密码，上面会有代理的ip）不要包含http://，端口也要填。
                    * 连接检查和配置成功后，会自动下载一些配置文件。然后在后面生成代码的时候，也会去下载MCU相应的firmware。
                    * 虽然也可以手动导入固件zip包，但官网上好像只有最新版，STM32CubeMX不一定能用最新版的，下载后，可以在Project Manager页面的最后取消使用默认固件位置，然后选择想要的版本的固件解压文件夹（下载时自动加压zip包）。
                * 先要选择芯片系列
                    * 左侧可以根据MCU或板子进行搜索，找到后可以修改一些配置，然后选中；也可以一级一级地手动选型号，从上到下是Core、Series、Line，当然也可以直接选下面具体的。
                    * 然后在左侧再做一些其他的配置，就可以在右侧的MCU列表上双击某行创建Project了，或者选中某行后点击右上角的创建按钮。
                    * 在Project Manager标签页，填上项目名称、项目路径、选择目标IDE（在"Toolchain / IDE"下面，生成代码时会生成对应的结构，比如选SW4STM32）
                * 配置界面
                    * 在Pinout & COnfiguration、Clock Configuration等标签页做配置。
                    * 点击菜单上的“GENERATE CODE”按钮（如果之前没有设置过网络连接，这里可能会让再设置一下，也可以点击继续，但是会少一些包，只生成源码）。结束后会有一个“Open Project”的按钮，可以直接用SW4STM32导入和打开项目，导入后其实也就是多了个.settings/language.settings.xml文件，是CDT插件的配置？
                * 生成的代码结构
                    * Inc文件夹：头文件文件夹，初始就只有main.h、stm32f4xx_hal_conf.h、stm32f4xx_it.h
                    * Src文件夹：源文件文件夹，初始就只有main.c、stm32f4xx_hal_conf.c、stm32f4xx_it.c
                    * .mxproject文件：头文件文件夹路径及文件列表、源文件文件夹路径及文件列表、使用的lib库文件LibFiles路径（多个）
                    * xxx.ioc文件：可视化做的那些配置，比如MCU、pin、clock的配置
            * STM32CubeMX用于配置串口中断等硬件的东西，一般不用改。
	        * 打开软件或生成代码时，需要配置连接/代理，用于自动下载配置文件和相应MCU的固件。proxy的地址直接写ip，不要带http://，端口也要填，SNC用的是140.231.192.162:8080，还要填上认证信息。
	        * 配置硬件之前需要先过滤和选择MCU型号，左侧用于过滤，右侧是符合条件的MCU列表用于选择。
	        * 最后自动生成项目代码时可以选择不同IDE，一般选择SW4STM32的代码结构用其进行后续开发，因为免费。
	        * 如果成功下载了相应MCU的固件（firmware），在生成的项目代码中会有相关文件夹和文件，否则只会生成源码部分。
        * SW4STM32
            * [SW4STM32官方界面](https://www.st.com/en/development-tools/sw4stm32.html)
            * 简介
                * 是意法（ST）公司官方提供的IDE
                * 专为STM32单片机开发服务
                * 支持标准外设库和HAL库驱动
                * 支持使用ST-LINK
                * 基于Eclipse的开源免费
                * 没有代码规模限制
                * 跨平台、使用GCC C/C++ compiler
                * 使用GDB-based debugger
            * 使用
                * [SW4Stm32开发环境搭建](https://blog.csdn.net/fishsmarkdown/article/details/53576775)：固件安装、stlink v2使用和手动复位问题修复
                * **[sw4stm32开发stm32](https://blog.csdn.net/weixin_39871788/article/details/80641997)**：stm32cubemx结合SW4Stm32
                * import现有工程后报错：C Error 127 occurred while running autoreconf
                    * 有的说需要[下载修复工具](http://www.saetechnologies.com/eclipse-c-error-127-occurred-while-running-autoreconf/)
                    * 因为FreeRTOS基于linux，编译时用到automake生成makefile，所以需要安装[automake](http://www.gnu.org/software/automake/)并在环境变量和SW4STM32中配好？那么就要mingw这种linux环境下安装？然后又依次间接需要autoconf、m4？[参考](https://blog.csdn.net/qq_30549833/article/details/72955881)
                    * 每个的安装都要在其目录下运行./configure –prefix=/usr/loca、make和make install
                    * [automake下载](http://ftp.gnu.org/gnu/automake/?C=M;O=D)
                    * [autoconf下载](http://mirrors.kernel.org/gnu/autoconf/)
                    * [m4下载](http://mirrors.kernel.org/gnu/m4/)
                * 如果正确安装了CubeMX和SW4STM32，工具栏会有一个小锤子按钮，用来build project。
                * 一定要把自己的代码写在/ * USER CODE BEGIN * /和/ * USER CODE END * /之间，否则CubeMX重新生成代码时会丢失代码。
* 调试/下载程序/仿真
    * JTAG（只是一种接口/协议？）
    * st link v2