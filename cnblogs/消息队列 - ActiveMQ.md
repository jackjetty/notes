* 支持MQTT及基于WebSocket的MQTT
    * example中有例子
* 配置
    * 用命令启动时可以指定使用的activemq的xml配置文件
    * activemq的xml配置文件中有协议和端口的配置，必须配置了，客户端才能以相应的协议连接上来
    * activemq的xml配置文件会引用jetty的配置文件，其中有web管理界面的端口，必须引用了才能使用web管理界面