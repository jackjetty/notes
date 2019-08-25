# 权限管理
* chmod语法为：chmod abc file，其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。

# 目录
* 通常软件的配置文件会被安装到/etc/xxx.conf下面
* 一般是把profile文件pid放到/var/run/xxx目录
* /etc
    * 通常会放一些程序的xxx.conf配置文件
    * /etc/init.d里面放的通常是开机启动的服务的配置

# 安装软件/服务
* 使用deb安装包安装
    * 安装：dpkg -I xxx.deb
    * 通常配置文件在/etc目录下
    * 有的软件（如Cloud Foundry Cli）会安装到home下的当前用户的文件夹下面，并不是通用的安装目录下
    * 安装完成后通常不需要再往/etc/profile下面增加export，直接运行程序命令即可
* 使用apt-get
    * 如果使用apt-get update时报错如connecting to cn.archive.ubuntu.com，一直是0%，那么可能是网不通，或者说是源连不上，可能要改一下软件包的源。
        * **还有可能和账号的环境变量等有关，比如用root运行apt-get命令时，不要再加sudo就好了，用sudo就会不通**
    * 修改软件包源：修改/etc/apt/source.list中的源地址
        * 网上可以找到，一般用网易、搜狐等的。
        * 保存后立即生效
    * 如果遇到如下报错，可能是之前的apt-get没有正常结束，需要杀掉进程
        * E: Could not get lock /var/lib/apt/lists/lock - open (11: Resource temporarily unavailable)
        * E: Unable to lock directory /var/lib/apt/lists/
        * 可以使用ps -aux命令查看sudo apt-get up和apt-get update的进程号，然后kill掉:sudo kill 2673    sudo kill 2674
* 自带Python
    * 自行安装2.7：sudo apt-get install python2.7 python2.7-dev

# 网络设置
* **Terminal中使用网络代理，需要设置http_proxy和https_proxy**，可以设置到/etc/profile中作为全局设置
    * 注意Linux上设置proxy环境变量时值要有双引号，window上没有。如export http_proxy="http://194.138.0.5:9400"
* 查看网络信息：ifconfig [网卡名字]
* 重启网络（也可手动）：sudo service network-manager restart
* 查看网卡配置：sudo gedit /etc/network/interfaces
* 设置DNS：/etc/resolvconf/resolv.conf.d/base
    * nameserver xxx.xxx.xxx.xxx
* 设置主机名：/etc/hosts

# 设置环境变量
* [ubuntu环境变量的三种设置方法](https://blog.csdn.net/vertor11/article/details/70799971)
* 为当前Terminal临时设置：直接在Terminal中export http_proxy=http://194.138.0.5:9400，
* 为当前用户设置：~/.bashrc中添加行：export http_proxy="http://194.138.0.5:9400"，**source .bashrc使其生效**
* 为所有用户设置：/etc/profile中添加行：export http_proxy="http://194.138.0.5:9400"，**source profile使其生效**

# 杂项
* 常用gedit编辑文件
* 给安装步骤中创建的用户分配管理员权限
* 安装完成后，会提醒你移除安装媒介后按一下回车才重启。
* 修改默认的root密码。默认root密码是随机的，即每次开机都有一个新的root密码。我们可以在终端输入命令 sudo passwd，然后输入当前用户的密码（如果已经输入过一次密码，这一步会跳过），enter。终端会提示我们输入新的密码并确认，此时的密码就是root新密码。修改成功后，输入命令 su root，再输入新的密码就ok了。
* 命令行要使用网络代理的话，需要在环境变量中设置http_proxy和https_proxy（和Windows类似）
* source只是让文件设置生效