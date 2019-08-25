* 安装方式
    * [Eclipse安装插件的三种方式](https://blog.csdn.net/qq_37502106/article/details/80566914)
    * 直接复制安装
        * 离线下载好插件，通常去这个插件的官网去找，就是在线安装的地址？如testng可以去http://beust.com/eclipse即http://dl.bintray.com/testng-team/testng-eclipse-release/下面下载zip包
        * 把插件所在文件夹（假设是python插件）中features与plugins文件夹中的内容分别复制到eclipse安装目录中的features与plugins文件夹中，然后按照前面的方法重启eclipse就好了。
    * 使用eclipse自带的图形界面的插件安装方法
        * help >> Install New Software >> Add >> 输入Name与Locaton
        * 这每一个Name与Location，就相当于一个插件及其对应的下载目录，名字应该可以随便取
    * 从eclipse的marketplace网站拖Install按钮到eclipse的界面中，如https://marketplace.eclipse.org/content/testng-eclipse
    * Help->Eclipse Marketplace中去搜索安装
* Eclipse的软件管理
    * Window->Preferences->Install/Update下面的Available Software Site中可以设置要下载插件/软件的源网站，可以有多个，可以取消勾选即Disable。这个和help >> Install New Software >>Manage弹出的内容是一样的。如果你用help >> Install New Software >> Add >>新增了插件/软件下载网站，那么会添加到这个列表中。