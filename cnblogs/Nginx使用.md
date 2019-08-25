#1. 基本使用
* 分linux和windows版
    * windows版可以直接双击exe运行，默认配置为80端口，只有两个页面
* html目录下为页面、css、js等代码文件
* conf目录下为配置文件
    * 主要的配置文件
        * conf/ngnix.conf。如果自编译的话，可以编译时在configure文件中修改名称和位置。
        * 其中有端口配置、http头配置（如缓存控制）、路由映射等