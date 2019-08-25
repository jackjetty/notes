* 可以使用devtools功能来实现热部署（Hot Swapping），需要加入依赖（如maven）：spring-boot-devtools
    * 可以实现修改代码并保存后的自动编译、重启
    * 依赖于Eclipse/Spring tool suite的project菜单下的Build Automatically要勾选上
    * 本质上这个工具是保存自动把java编译成class文件，然后用Eclipse的Build Automatically来自动检测class文件的变化并重启
* 启动Spring Boot项目
    * 使用命令行mvn spring-boot:run
    * 右击启动类后选Run或Debug As Java Application
    * 右击项目，选Run As，选Maven Build，在弹窗中输入Goals（如spring-boot:run）然后点Run，其实就是新建了一个启动的Application？？其实就是第一种命令行模式的图形化？？