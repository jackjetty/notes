#1. 概述
* Nagios负责收集数据，是一款开源的免费网络监视工具。
* influxDB负责存储数据，是一个开源的时间序列数据库。比较适合存储监控或者部署记录这些时序数据。
* Grafana负责数据的图形化展示，是一款跨平台的开源的度量分析和可视化工具。大多用于时序数据的监控方面。
* [nagios+influxdb+grafana的监控数据可视化流程 ](https://www.cnblogs.com/howtobuildjenkins/p/9141092.html)

#2. Nagios
* 官网
    * [Nagios官网](https://www.nagios.com/)
    * [服务端下载界面](https://www.nagios.org/downloads/nagios-core/thanks/?skip=1&product_download=nagioscore-source)
    * [服务端安装手册](https://support.nagios.com/kb/article/nagios-core-installing-nagios-core-from-source-96.html#_ga=2.106174276.675129037.1551081939-2099575042.1550552846)
        * 找对目标操作系统及其版本的安装步骤
    * [服务端插件安装手册](https://support.nagios.com/kb/article/nagios-plugins-installing-nagios-plugins-from-source-569.html)
    * [客户端下载界面](http://nsclient.org/download/)
    * [nagioscore文档 - v3](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/3/en/toc.html)
    * [nagioscore文档 - v4](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/toc.html)
* [Nagios 监控](https://www.cnblogs.com/kaituorensheng/p/4682565.html)
* [Nagios部署与配置 ](https://www.cnblogs.com/bixiaoyu/p/6915763.html)
* 分为免费版的Nagios Core和企业版的Nagios XI
* 功能
    * 能有效监控Windows、Linux和Unix的主机状态，交换机路由器等网络设备，打印机等。
    * 在系统或服务状态异常时发出**邮件或短信报警**第一时间通知网站运维人员，在状态恢复后发出**正常的邮件或短信通知**。
    * 也可以以**插件**的形式自己编写脚本采集和发送数据，Nagios有自己的一套逻辑去按需触发它。
* 服务端（Nagios Core）安装
    * Nagios Core可安装在RHEL、Ubuntu、CentOS等平台。
    * 默认安装目录：/usr/local/nagios
* Nagios在监控服务的性能和状态时，有以下几个概念：
    * Host
    * Host Group
    * Service
    * Service Group（AMP没用到）
* Nagios Plugin Development（自定义插件开发）
    * [Nagios Plugin API - v3](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/3/en/pluginapi.html)
    * [Nagios Plugin API - v4](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/pluginapi.html)
    * [Nagios Plugins Development Guidelines - v4](https://nagios-plugins.org/doc/guidelines.html#_ga=2.268230606.1510805352.1551766248-2099575042.1550552846)
    * 总结（假设安装目录是/user/local/Nagios）
        * **Nagios在安装时，官方手册有一步会调用源码中的命令创建服务的用户和用户组，都是nagios。web的账号是nagiosadmin**
        * 插件运行在Nagios服务端
        * 插件的运行入口是一个cfg配置文件（/etc/objects/template.cfg是一个官方提供的demo）。
            * 要在安装目录下的/etc/nagios.cfg中注册该入口配置文件：cfg_file=/usr/local/nagios/etc/objects/mindsphere_MTA.cfg，然后重启。
                * 重启：sudo systemctl restart nagios.service
            * 其中定义了用到的command、service、host、hostgroup，供Nagios的web使用，可以在界面上查看和操作这些元素。
                * hostgroup
                    * 定义中的members部分定义了包含哪些host，members字段中指定的host必须也要定义好，不然虽然页面不报错，但是连这个hostgroup也显示不出来
        * 查看日志
            * /usr/local/nagios/var/下面，包括archives子目录
            * 如果启动成功了，那么具体的错误日志在界面上到相应的service的Status Information中也可以看到
        * Nagios的服务分为网站和后台服务两部分，sudo systemctl restart nagios.service操作的是所有服务，sudo service nagios stop关的只是后台服务。

```
# HOSTGROUP DEFINITION ##############################################
define hostgroup {
        hostgroup_name  MMM-V3
        alias           MindSphere MTA App
        members         PRODA-MMMDEV
}
```

                * host
                    * 定义中的contact_groups和contacts部分定义了联系人和联系组

```
define host {
        use             generic-host
        host_name       PRODA-MMMDEV
        alias           ProdA-MMMDEV
        address         127.0.0.1
        max_check_attempts 1
        action_url      null
        notes_url       null
        icon_image      cf.png
        icon_image_alt  CloudFoundry
        notes           MMM running in MMMDEV Space on PRODA
        contact_groups  MTALive,MMMViewUsers,admins
		contacts		DevOps
}
```
                    * 这两个字段中用到的contact必须在/etc/objects/**contacts.cfg**（也可以放在自己这个配置文件中？？）中定义好，不然启动就会报错。

```
define contact {

    contact_name            nagiosadmin             ; Short name of user
    use                     generic-contact         ; Inherit default values from generic-contact template (defined above)
    alias                   Nagios Admin            ; Full name of user
    email                   nagios@localhost ; <<***** CHANGE THIS TO YOUR EMAIL ADDRESS ******
}

define contactgroup {

    contactgroup_name       MTALive
    alias                   MTALive
    members                 DevOps
}
```
                * command

```
define command {
        command_name    check_MMMservice_ProdA-MMMOPS
        command_line    /usr/local/nagios/myplugin/check_proda_apps.sh $ARG1$ $ARG2$
}
```

                * service

```
define service {
        use             generic-service
        host_name       PRODA-MMMDEV
        service_description SinumerikAlarmProtocol
        check_interval  2
        retry_interval  1
        check_command   check_MMMservice_ProdA-MMMOPS!SinumerikAlarmProtocol!mcapps
        notes           Checks status of service alarmprotocol on ProdA MMMOPS system
        contact_groups  MTALive
		contacts        DevOps
        notifications_enabled       1
        notification_period          24x7
        notification_options         w,c,r,f,s
}
```

#3. influxDB
* 官网
    * [influxDB官网](https://www.influxdata.com/)
    * [influxDB官网下载界面，提供了各个平台的安装命令](https://portal.influxdata.com/downloads/)
* [InfluxDB基本概念和操作](https://www.cnblogs.com/shhnwangjian/p/6897216.html?utm_source=itdadao&utm_medium=referral)
* [windows版influxDB安装与配置](https://blog.csdn.net/xiaocxyczh/article/details/78682211)
* [influxDB1.6版安装与配置(windows环境)、Jmeter+influxDB+Grafana性能监控](https://blog.csdn.net/SwTesting/article/details/82700023)
* 也有自带的web界面-Chronograf，也在官网下载。
* 使用
    * influxdb
        * **添加influxdb解压包目录的环境变量后，命令行中用influxd打开influxdb服务，默认是8086端口。默认账号密码都是admin？小心别把命令行阻塞了，会导致各种操作暂停**。
        * influxdb.conf配置文件可以配置data、meta、wal三个目录的位置，端口等。
        * Linux
            * 启动服务：sudo influxd
                * **为什么一定要占用Terminal前台？有没有后台service运行的方式，类似nagios？**
            * 默认安装目录：/usr/bin/influx、influxd等，/usr/lib/influxdb
            * 默认配置文件位置：/etc/influxdb/influxdb.conf
            * 启动命令行：和Windows一样
    * Chronograf
        * 运行Chronograf解压目录下的chronograf.exe运行Chronograf服务，默认是8888端口，可以在浏览器中打开操作
* 数据库操作方式
    * 客户端命令行方式
        * 进入命令行：influx 、或者指定时间戳格式influx -precision rfc3339，或者打开influx.exe
        * 常用命令
            * 数据库操作
                * SHOW DATABASES
                * CREATE DATABASE "db_name"
                * DROP DATABASE "db_name"
                * USE "db_name"
            * 表操作
                * SHOW MEASUREMENTS ON "db_name"
                * insert "measurement_name","tag_name"=server01 value=442221834240i [选填1435362189575692182时间戳]
                    * **InfluxDB中没有显式的新建表的语句，只能通过insert数据的方式来建立新表。**
                    * DROP MEASUREMENT "measurement_name"
            * 数据操作
                * insert "measurement_name","tag_name"=server01 value=442221834240i [选填1435362189575692182时间戳]
                * 查询语句与SQL一样
                * **不能修改/删除数据**
            * series操作
                * series表示这个表里面的数据，可以在图表上画成几条线，series主要通过tags排列组合算出来。
                * show series from measurement_name
    * 备份与恢复
        * [InfluxDB数据备份和恢复](https://www.cnblogs.com/zihanxing/p/7405414.html)
    * HTTP API接口
    * 各语言API库
    * 基于WEB管理页面操作

#4. Grafana
* 官网
    * [Grafana官网](https://grafana.com/)
    * [Grafana官网下载界面](https://grafana.com/grafana/download?platform=windows)
    * [Installing on Windows](http://docs.grafana.org/installation/windows/)
* [可视化工具Grafana：简介及安装](https://www.cnblogs.com/imyalost/p/9873641.html)
* [Grafana学习笔记](https://blog.csdn.net/vtnews/article/details/80191089)
* 功能
    * 支持Elasticsearch、MySQL、Sql Server、PostgreSQL、influxDB、Graphite等多种数据源，以及多种查询语言。
    * 支持多种图表及各种显示设置，数据可基于查询语言。
    * 支持自定义界面布局。
    * 支持通知功能。
    * **支持以json格式导入导出界面设置，包含数据源、query、界面、alert、定时刷新等所有设置。**
* 使用（Dashboards->Home网页上会指导你一步步进行）
    * Install Grafana
    * Create your first data source
    * New dashboard
    * Invite your team
    * Install apps & plugins
* 操作
    * **启动服务：运行grafana-server.exe（linux上是service grafana start），默认是3000端口，默认账号密码都是admin，第一次登陆后会让你设置新密码**
    * 和Graphite类似，也是可以有多个Dashboard，每个Dashboard中可以自定义布局和使用哪些图形，新建一个Dashboard。
        * 在Dashboards页面可以导入panel，使用json文件或直接输入json配置
        * 在每个dashboard的配置页面可以修改当前的json配置
    * 在新的Dashboard中可以增加不同类型的图形。每个图形的title点Edit，在Metrics标签页可以拼接一个完整的查询语句来控制图表展示的数据。
    * 每一个页面是一个**Dashboard**，可以设置布局，包含多个panel 
   * 每个图形是一个**panel**，点击标题后下单菜单的Edit可以进一步设置
        * 第一个面板设置Queries，可以设置数据源及查询等
        * 第二个面板设置Visualization，可以设置显示成点、线（**默认不显示空值的话，会显示成一个个的点，要把Null value设成connected，才会忽略空值将点连成线**）还是饼，还可以设置Axes、Legend等。
        * 第三个面板设置General，如标题什么的
        * 第四个面板设置Alert。**重要实用，项目也在用**