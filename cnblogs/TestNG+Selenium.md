* 是一个开源自动化测试框架。其实类似于JUnit这种单元测试框架，但进行了一些功能扩展
* 属于selenium？还是说TestNG是一个测试框架，它用到了selenium的web自动化测试的功能，比如使用浏览器对应的driver去进行操作，
* 不一定要由RobotFramework触发
* 参考
    * [TestNG环境设置（配置安装）](https://www.yiibai.com/html/testng/2013/0914293.html)
    * [浅析基于Selenium和TestNG的自动化测试框架](http://www.51testing.com/html/40/n-3721140.html)
* 使用
    * 可以使用多种语言，下面以java语言为例，使用Chrome+编写抓取页面的逻辑
    * 流程
        * 新建一个java项目，可以是gradle项目
        * 新建测试类，**使用testng提供的一些注解如@Test**，打包成jar包待用（需要包含依赖的jar包，比如自定义gradle task创建完整的jar包）
            * 貌似简单情况下有编译完的class文件即可？
        * 新建一个testng的xml配置文件，其中可以指定测试类包全路径、一些文件路径、变量、执行测试类中的所有方法还是指定方法等。比如打开页面后要输入的账号密码等，java代码中可以使用。
            * **如果xml中包含url之类的参数，如果其中有.等特殊字符，需要进行urlencode。如果有&等特殊字符，需要进行xml转义。**
        * 使用java命令触发执行测试方法
            * 可以简单的使用java org.testng.TestNG testng.xml，其中第一个参数通常是固定的，表明使用testng执行测试；第二个参数是配置文件的名称
                * 这里其实是默认了testng的jar包、测试脚本的jar包或class文件、xml配置文件都在执行命令的cmd当前目录下
            * 比较复杂的情况下，如果这些文件不在同一目录下，可以使用java命令的-cp参数（其实就是-classpath参数）指定搜索testng的jar包、测试脚本的jar包的目录（可以多个，用:分隔，路径中可以使用\*这种匹配符），xml配置文件可以直接在java命令中写上全路径
                * 如C:\TestNG_WORKSPACE>java -cp "C:\TestNG_WORKSPACE" org.testng.TestNG testng.xml
            * 通常作为一个进程启动，脚本（比如.sh脚本）结束前要把这个进程杀掉。
    * 如果是按分辨率抓取信息，那么通常在执行命令前要进行屏幕（浏览器？）分辨率的设置，即使java等语言的测试类代码中是根据页面元素id来抓取的
    * 最好去[selenium官网](https://www.seleniumhq.org/download/)下载驱动，并且查看**驱动、浏览器、selenium三者之间的版本对应关系，一定要对应上**。
        * 版本对应关系可以百度搜，也可以去官网提供的GitHub仓库里去看各个release的release note，如https://github.com/mozilla/geckodriver/releases。
    * 不需要指定浏览器的目录，需要指定driver文件的路径（需要指定系统属性如：System.setProperty("webdriver.gecko.driver", "/mmm/SeleniumLib/geckodriver");）。
    * selenium提供了需要操作页面元素的方法，比如getElement之类的
        * 找到Element后，可以获取内容，可以填充文本，还可以调用click()方法去点击按钮
        * 和scrapy类似，用的也是xpath去找的html元素，可以在火狐上装xpath的测试插件辅助开发
    * 使用Selenium+chromedirver自动化操作Chrome
        * Ubuntu上有可能出于版本等原因无法使用Selenium+chromedriver自动化操作Chrome
        * 即使java代码中使用chromedriver启动Chrome时不使用AddParameter()添加--headless参数，也无法打开界面
        * 有时需要在启动Chrome时传入一些参数关闭sandbox（可以在java等语言的测试类代码中使用chromedriver启动chrome时设置），才能正常使用。
    * 使用Selenium+geckodriver自动化操作Firefox
        * 脚本需要有足够的权限（比如root），否则即使直接运行Firefox命令都会报错，报错信息通常是Connection Refused、Can't open display: :0之类乱七八糟的错误。
        * Firefox不像Chrome那样是通过参数自定义设置，而是通过Profile，每个Profile包含一系列用户的设置（**是不是下面的代理弹窗问题，也可以使用Profile解决？虽然java代码中Profile类没有代理账号密码之类的字段，但是可以自己打开浏览器输入一次，然后导出profile，让selenium去读和使用？貌似没有设置账号密码的preference。。。。难道是signon.autologin.proxy！？**）。
            * [获取FirefoxProfile配置文件以及使用方法介绍](https://www.cnblogs.com/moonpool/p/5659128.html)：**貌似%APPDATA%\Mozilla\Firefox\Profiles\prefs.js就是这个配置文件**，内容都是user_pref("network.proxy.type", 1);这样的preference设置。**也可以在火狐浏览器中地址栏输入about:config打开查看**
            * 这些Preference的设置也可以参考官方文档：http://kb.mozillazine.org/Network.proxy.type
            * [Selenium FF WebDriver 加载firebug 和设置代理](https://www.cnblogs.com/tobecrazy/p/3997375.html)
            * **貌似selenium+geckodriver每次都是用了一个新的空的Profile，除非在代码中创建和设置一个新的FirefoxProfile，并且在初始化driver时传入，如profile.setPreference("network.proxy.type", 1)。**
            * **network.proxy.type比较特别，是个枚举，对应几种不同的代理设置方式**
        * 特殊情况下，比如特殊的公司网络下，浏览器在访问网页时需要使用代理服务器（可能是固定的，也可能是pac脚本动态判断的），这是有可能代理服务器会让你输入域账户的账号密码。这个很难处理（不处理的话就卡在这里了，没发进一步操作页面），selenium和geckodriver只提供了配置代理服务器的方式、地址、端口的变量，并没有提供设置账号密码的地方。网上有人说就是不支持。然后以前有个close-proxy-authentication的火狐插件也已经下架不能用了。自己写脚本往弹窗里填充有点困难。。。不是一个html页面。。。要抓包看看了？
        * 因为[公司的代理服务器做了认证的设置？](https://www.cnblogs.com/wj78080458/p/10060941.html)
        * **尝试设置系统网络的自动完成功能？**
        * **Windows的话尝试使用Windows凭证？**
        * 可以使用前面Profile的办法，找到prefs.js文件查看有没有手动在浏览器中设置的账号密码，有的话就用到代码中
        * 还有一个可能：火狐的代理设置页面的下面有一个选项“如果密码已保存，不提示身份验证(I)”，是不是勾选了就行了？即使selenium启动的火狐是一个全新的profile，也可以在prefs.js文件中找到相关的配置，配到代码中？
        * **终于解决了！！！！！！！！！！在全局的cmd的环境变量中添加代理的认证信息，export http_proxy_user=”” and export http_proxy_pass=””，有可能同时要使用保存的账号密码，有可能代理要用系统设置**