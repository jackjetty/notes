#参考
* [关于MQTT协议实现消息推送系统](https://blog.csdn.net/u013870094/article/details/78992435)
* [MQTT与kafka对比分析](https://blog.csdn.net/yeshenzzrff/article/details/79021479)
* [[经验] 物联网项目开发必读 深度分析MQTT协议优缺点](https://blog.csdn.net/qq_29864185/article/details/54669693)
* [MQTT Mosquitto安装和使用](https://blog.csdn.net/a5nan/article/details/76890180)
* [MQTT协议_连接](https://blog.csdn.net/putiancaijunyu/article/details/79269369)

#1. 为什么使用MQTT协议
* 协议包含语法、语义、语序。
* 基本上互联网上的各种应用协议都是基于TCP/IP协议，但根据不同的应用业务/场景需求，产生了众多应用层协议。
    * XMPP协议的目的是双向实时的通信，因此要定义心跳包、房间等众多概念及相应的交互过程
    * HTTP协议的目的是网页浏览和交互，通常是1对1、单向、同步、网络相对稳定，业务复杂因此头部复杂（Method、缓存、认证什么乱七八糟的）
* 对于物联网领域来说，通常网络不稳定、流量少、有广播消息的场景，这些都是常见的HTTP协议不具备的特性，因此：
    * MQTT协议的目的是支持广播、心跳包、轻量级、异步、支持双向等。

#2. 服务端-mosquitto
* [官网](https://mosquitto.org/)
* [GitHub](https://github.com/eclipse/mosquitto)
* 开源的Eclipse mosquitto是一个基于C++的MQTT message broker，相当于是一个服务端代理。
* [Mosquitto简介及搭建](https://blog.csdn.net/z729685731/article/details/70142154)
* [MQTT Mosquitto安装和使用](https://blog.csdn.net/a5nan/article/details/76890180)
* 默认broker端口是1883（常用）
* 安装
    * 需要openssl，并且手动拷一个libssl-1_1-x64.dll到mosquitto的目录，[下载地址](http://slproweb.com/products/Win32OpenSSL.html)，[Windows安装使用Openssl](https://blog.csdn.net/zha6476003/article/details/80900988)

#3. 服务端-Apache Apollo
* [官网](http://activemq.apache.org/apollo/)
* 也是一个message broker，支持包含MQTT在内的众多协议，**有web页面**
* [安装MQTT服务器(Windows),并连接测试](https://www.cnblogs.com/yangfengwu/p/7764667.html)
* 使用
    * 下载解压
    * 根目录cmd执行命令apollo.cmd create xxx，创建目录（**xxx为Virtual Host名称，算是最大的一个管理单位**）
    * 进入xxx/bin目录，执行命令apollo-brokder.cmd run，启动服务，即可浏览器访问https://127.0.0.1:61681/或http://127.0.0.1:61680/，默认账号密码就是admin和password
    * 可在apollo.xml配置文件中修改端口（注意让别人访问需要新建打开防火墙规则）、账号、密码，也可以在管理界面直接改

#4. 客户端-MQTT.fx
* [官网](http://www.mqttfx.org/)
* [下载地址](http://www.jensd.de/apps/mqttfx/1.7.1/)，[第三方官网](https://www.jensd.de/wordpress/)
* 一个基于JavaFX的客户端
* [MQTT入门（4）- 客户端工具](https://rensanning.iteye.com/blog/2406598)
* [mqtt.fx的安装和使用](https://blog.csdn.net/nicholaszao/article/details/79211965)
* **默认想要连接的本地broker端口是1883，如果用的是apollo，需要改为apollo.xml配置文件默认的61613（用的是apollo的tcp端口，不是管理web界面用的61680和61681端口），同时需要配置连接用的账号密码**

#5. 客户端-通信猫调试软件
* [安装MQTT服务器(Windows),并连接测试](https://www.cnblogs.com/yangfengwu/p/7764667.html)

#6. 功能
* 客户端可以publish消息到某个topic也可以subscribe某个topic，也可以创建topic（向一个不存在topic发送一次消息，不知道是不是后台帮忙做的创建动作？）
* 服务端/broker可以创建topic、queue，可以查看消息量，可以查看有哪些消费者
* 每个消息都可以选择不同的QoS