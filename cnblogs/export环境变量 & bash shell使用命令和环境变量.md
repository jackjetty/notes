* export环境变量
    * export命令
        * 在Terminal中可以使用export命令查看所有环境变量，可以使用export varname=xxx来设置环境变量，可以使用export -n varname来删除环境变量
        * export中的环境变量，可能是直接命令设置的，也可能是后文提到的各个配置文件（有优先级）中过来的
        * 但是export的作用范围只是当前Terminal，重启Terminal后还是会出现
    * 关于各个文件的作用域**（修改后，都要使用source filename来使之生效？）**，在网上找到了以下说明：
        * /etc/profile： 此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行. 并从/etc/profile.d目录的配置文件中搜集shell的设置。
        * /etc/bashrc: 为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取。
        * ~/.bash_profile: 每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。
        * ~/.bashrc: 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。
        * ~/.bash_logout: 当每次退出系统(退出bash shell)时,执行该文件. 另外,/etc/profile中设定的变量(全局)的可以作用于任何用户,而~/.bashrc等中设定的变量(局部)只能继承 /etc/profile中的变量,他们是"父子"关系。
        * ~/.bash_profile 是交互式、login 方式进入 bash 运行的~/.bashrc 是交互式 non-login 方式进入 bash 运行的通常二者设置大致相同，所以通常前者会调用后者。
* bash shell使用命令和环境变量
    * root是没有~/.bashrc的，只. /etc/profile即可，**/etc/profile和~/.bashrc的作用类似，只是作用域不同，都是写死的export，也有动态的脚本去设置命令和环境变量**
    * 执行顺序
        * 关于登录linux时，/etc/profile、~/.bash_profile等几个文件的执行过程。 在登录Linux时要执行文件的过程如下： 在刚登录Linux时，首先启动 /etc/profile 文件，然后再启动用户目录下的 ~/.bash_profile、 ~/.bash_login或 ~/.profile文件中的其中一个，执行的顺序为：~/.bash_profile、 ~/.bash_login、 ~/.profile。如果 ~/.bash_profile文件存在的话，一般还会执行 ~/.bashrc文件。 从~/.bash_profile和~/.bashrc的代码可以看出，~/.bashrc会调用 /etc/bashrc文件。最后，在退出shell时，还会执行 ~/.bash_logout文件。
        * 执行顺序为：/etc/profile -> (~/.bash_profile | ~/.bash_login | ~/.profile) -> ~/.bashrc -> /etc/bashrc -> ~/.bash_logout
    * 用useradd命令创建的用户通常会共用现有的bash，但有时如安装Nagios时用他的脚本生成的用户，可能会没有自己的bash，需要从root的~/.bashrc拷一份到/home/xxx下面
    * 比如报错说/home/xxx/.bashrc: Permission denied 问题，可能就是当前用户没有成功共用bash shell设置，需要修改正确/home/xxx的权限，然后拷贝一份~/.bashrc过来，**但是这种方式需要每次登陆后source ~/.bashrc一下才能让这个用户级的bash shell设置生效**
    * 比如ll命令就是ls命令的alias name，在bashrc中可以看到，如果bash有问题，可能会报错ll: command not found
    * **执行命令时如果有莫名其妙的错误，要用whereis确认一下调用的到底是不是想要的命令**
    * 经常在sh脚本文件第一行加上#!/bin/bash，表示需要使用的bash shell环境
        * [关于#!/bin/bash和#!/bin/sh](https://www.cnblogs.com/xiaojianblogs/p/8980558.html)