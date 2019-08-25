* [st官网](https://www.st.com/content/st_com/en/products/development-tools/hardware-development-tools/development-tool-hardware-for-mcus/debug-hardware-for-mcus/debug-hardware-for-stm32-mcus/st-link-v2.html#design-scroll)
* [正版ST-link/V2引脚定义和注意事项](https://blog.csdn.net/xinghuanmeiying/article/details/78026561)
* 分为ST-LINK/V2和ST-LINK/V2-ISOL两种型号
* 是STM8和STM32微控制器（MCU）系列的在线调试器和编程器（还是下载器、仿真器？？）。
* 使用
    * keil
        * [ST-LINK_V2使用说明](https://wenku.baidu.com/view/4b2ce6debcd126fff6050b9a.html)
    * sw4stm32
        * [sw4stm32开发stm32](https://blog.csdn.net/weixin_39871788/article/details/80641997)
    * STVP少些软件
        * [如何用ST-LINK V2给stm32下载程序](https://jingyan.baidu.com/article/f25ef254ad2aa1482c1b8223.html)
* 连接
    * 一头是usb与电脑上的开发软件连接？
    * 一头是一些特殊引脚（定义好的，有几根线/引脚必须接），与开发板上的STM32通过JTAG/SWD接口连接
* jlink仿真器