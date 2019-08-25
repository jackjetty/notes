#1. 概述
* 有时由于添加了自己编写的c++模块，或者改了源码的一些名称、配置什么的，需要自行编译nginx。
* 可以先下下来源码，然后需要的话就自己改下源码或增加模块，最后再编译成linux或者windows平台来用。
* 参考
    * [官方手册](http://nginx.org/en/docs/howto_build_on_win32.html)
    * [编译windows版nginx-rtmp-module](http://bashell.nodemedia.cn/archives/build-nginx-rtmp-module-on-windows.html)
    * [Windows编译Nginx源码](https://www.cnblogs.com/teamblog/p/6128460.html)
    * [Windows上编译Nginx](https://blog.csdn.net/pi314pi/article/details/81167814)
    * [windows编译nginx超详细教程](https://blog.csdn.net/i348018533/article/details/51701865)

#2. 在Windows上编译
* 准备（zlib、pcre、openssl可以统一放在objs/libs下面，后面的执行的configure脚本文件可以指定目录）
    * nginx源码：
        * 可以用[Mercurial源码下载器，自行下载 64位 or 32 位](https://www.mercurial-scm.org/)，在其安装目录下（或者加到环境变量Path中）运行命令：hg clone http://hg.nginx.org/nginx
        * 或者去[网站下载地址](http://hg.nginx.org/nginx)，[Github地址](https://github.com/nginx/nginx)
        * 比如用于生成Makefile的configure文件在不同版本的源码目录结构中位置有所不同。github上不在根目录下，而在auto文件夹下面。
        * 有的版本可能会出错
    * perl环境：安装ActivePerl或者Strawberry Perl，ssl环境，自行下载 64位 or 32 位
    * zlib：ssl相关，库函数，比如用1.2.11
    * PCRE：ssl相关，库函数，比如用8.42，[下载地址](https://ftp.pcre.org/pub/pcre/)
    * OpenSSL：版本不要太高，否则可能出错，比如用1.0.1s
    * MSYS（GNU环境）
        * 可以用[MinGW](https://sourceforge.net/projects/mingw/files/Installer/)去下载，然后运行**msys.bat**。下载mingw-get-setup.exe直接执行直接下一步即可，安装完成之后找到文件bin/mingw-get.exe打开，用这个来安装msys点击左侧的Basic Setup右侧选择mingw32-base和msys.base只有点击菜单isntallation－》Apply Changes 开始下载，完成之后点击左侧All Packages－》MSYS－》MSY Base System检查右侧菜单的选中组件中是否有msys-make、msys-tar因为这两个组件会在接下来用到。
        * 也可以去[Sourceforge](http://nchc.dl.sourceforge.net/project/mingw/MSYS/Base/msys-core/msys-1.0.11/MSYS-1.0.11.exe)下载，或者用[msys2](https://www.msys2.org/)
    * Visual Studio，会用到其VS2012 x86 Native Tools Command Prompt工具
* 创建编译命令集
    * 在Nginx根目录下，创建build.bat
    * 在build.bat中执行configure时，要指定编译根目录--prefix=D:/tmp/nginx-release-1.14.2/objs \（也可以直接改configure文件的NGX_PREFIX=${NGX_PREFIX:-/usr/local/nginx}）
    * 内容为（不同版本nginx的**configure文件--shell脚本文件**，位置不同：有的在根目录有的在auto目录下；注意修改依赖的库的路径；官网demo上的命令略有不同）：
```
configure \
    --with-cc=cl \
    --with-debug \
    --prefix=D:/tmp/nginx-1.12.2/objs \
    --conf-path=conf/nginx.conf \
    --pid-path=logs/nginx.pid \
    --http-log-path=logs/access.log \
    --error-log-path=logs/error.log \
    --sbin-path=nginx.exe \
    --http-client-body-temp-path=temp/client_body_temp \
    --http-proxy-temp-path=temp/proxy_temp \
    --http-fastcgi-temp-path=temp/fastcgi_temp \
    --http-scgi-temp-path=temp/scgi_temp \
    --http-uwsgi-temp-path=temp/uwsgi_temp \
    --with-cc-opt=-DFD_SETSIZE=1024 \
    --with-pcre=objs/lib/pcre-8.42 \
    --with-zlib=objs/lib/zlib-1.2.11 \
    --with-openssl=objs/lib/openssl-OpenSSL_1_0_1s \
    --with-openssl-opt=no-asm \
    --with-select_module \
    --with-http_ssl_module
```
* 生成MakeFile资源（Makefile及其他文件）
    * 运行MSYS（安装完成后，自动在桌面有快捷方式）切换到Nginx根目录（linux风格的路径，如/d/tmp/xxx），执行以下命令：**build.bat**
    * 如果使用MinGW装的GNU环境，那么可以运行**msys.bat**
* 编译Nginx源码（过程1分半钟）
    * 运行VS命令行（比如VS2012为其目录下的VS2012 x86 Native Tools Command Prompt，也可能是x64的），切换到Nginx根目录，执行以下命令：**nmake -f objs/Makefile**
* 编译完成后，objs目录下会生成nginx.exe

#3. 可能遇到的问题
* 'sed' is not recognized as an internal or external command,
    * 编译源码时发生，貌似无关紧要，sed.exe也是mingw下面的一个程序，可以加到环境变量中：C:\MinGW\msys\1.0\bin
* 运行生成的nginx.exe会报错：NMAKE : fatal error U1073: don't know how to make 'src/os/win32/ngx_win32_config.h'
    * 有时下载的源码包的/src/os下面没有win32目录及文件
    * [GitHub](https://github.com/nginx/nginx/releases/tag/release-1.12.2)上面有
    * 不行把其他版本呢上的拷过来了，比如1.14.2
* The system cannot find the path specified（可能是error.log、conf/nginx.conf、temp/client_body_temp等）
    * 需要手动在编译目录（objs）下面创建logs、temp、tmp、conf文件夹，并在conf文件夹中放一个可用的nginx.conf文件（如果用到了mime type，可能还要拷贝一个mime.types进来，当然还要建一个html文件夹并放入默认页面）