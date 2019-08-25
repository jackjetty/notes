* [SourceForge页面](https://sourceforge.net/projects/cntlm)
* 作用是转发请求到代理
    * 可以同时加上域验证信息（为各种NTLM认证？）
    * gradle、cmd、Terminal等工具或软件可能无法或者说没有使用系统/浏览器设置的代理，因为可以用Cntlm来为其把请求转发到指定的代理服务器。
    * **其实最重要的作用是附带ntlm认证信息，因为其实各个工具也都可以自己去设置代理，只不过可能只能设置一个ip，如果代理服务器要求NTLM认证，就只能用cntlm了**
* 安装后可以在cntlm.ini（Windows为例）配置文件中配置如下信息
    * 认证信息
        * 运行cntlm -H命令可以根据配置文件中设置的账号生成各个NTLM认证的认证信息，也可以在运行命令时加上这些参数
    * 转发到哪些代理服务器
    * 到哪些地址的请求不需要转发到代理服务器
    * 端口（比如公司的win10上默认的31288端口可能被占用）
* 使用
    * Windows
        * 从服务中运行
        * net start cntlm
       * 通常安装在C:\Program Files (x86)\Cntlm
    * Linux
        * service cntlm start
        * cntlm直接就可以启动服务，可能要加sudo
        * cntlm -v可以看到启动日志
        * killall cntlm就可以杀掉服务
        * 配置文件在/etc/cntlm.conf
        * 通常安装在sbin目录下面