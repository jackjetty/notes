* Ubuntu安装Chrome可以用火狐去谷歌官网下载deb安装包，然后用命令dpkg -I google-chrome-stable_current_amd64.deb来安装
    * 也有别的办法，[参考1](https://blog.csdn.net/qq551551/article/details/78885704/)，[参考2](https://jingyan.baidu.com/article/335530da98061b19cb41c31d.html)。需要先下载谷歌的列表，再添加谷歌的密钥，再apt-get update，再apt-get install google-chrome-stable，再运行/usr/bin/google-chrome-stable。
* ./xxx.sh可以运行sh脚本