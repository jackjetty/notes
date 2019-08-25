#1. 概述
* 参考
    * [Nginx开发HTTP模块入门](https://www.cnblogs.com/jianbingguozi/p/6938884.html)

#2. 扩展模块（假设根目录名称为nginx）
* nginx/configure文件中可以修改配置文件路径（编译时使用）：
    * 例如像把配置文件从默认的nginx.conf改为webserver.conf：NGX_PREFIX=${NGX_PREFIX:-/usr/local/nginx}改为NGX_PREFIX=${NGX_PREFIX:-/xxx/xxx/xxx}
* nginx/src/core/ngx_conf.c中的ngx_conf_full_name是配置文件名的配置？
* nginx/src/core/ngx_log.c中是日志的配置？
* 处理流程
    * 各个模块中会以固定格式，定义一些结构体，ngx_http_core_loc_conf_t* clcf;并设置一个clcf->handler
    * 先根据nginx/auto/modules中注册的模块，到（ngx_http_apptest_handler、xngx_recom_handler、ngx_recom_handler方法？怎么判断转发到哪一个模块？
    * 然后每个模块都初始化一个自定义的Entry类（相当于Web api中的路由转发设置，只是自行进行了解析url和转发调用各个处理函数），用于做路由转发（根据url调用不同的逻辑处理类）

#3. 项目学习
* 为什么把名称中的nginx改为了webserver？**因为用了BSD协议，随便改？？**
    * 为什么把默认引用的<nginx.h>都改成了#include "../../core/webserver.h"？当然也就同时要修改使用的地方如nginx/auto/sources
        * nginx/src/core/ngx_module.h
        * nginx/src/stream/ngx_stream_variables.c
        * nginx/src/os/unix/ngx_posix_init.c
        * nginx/src/http/v2/ngx_http_v2_filter_module.c
        * nginx/src/http/ngx_http_header_filter_module.c
        * nginx/src/http/ngx_http_special_response.c
    * 为什么把Copyright中的Nginx都改成了webserver？
        * nginx/auto/cc/conf等
    * 为什么把core下面nginx改成了webserver？
* 定制修改的文件（动了逻辑的）
    * nginx/conf（修改配置文件位置）
    * nginx/auto/cc/gcc
    * nginx/auto/lib/conf + nginx/auto/lib/app/conf（前者引用后者）
    * nginx/auto/install（调整安装过程，如拷贝一些配置文件到指定位置）
    * nginx/auto/make（编译相关，如g++）
    * nginx/auto/modules（注册自定义及使用到的扩展模块）
    * nginx/auto/options（一些nginx开关，如是否打开代理、重写等）
    * nginx/src/core/ngx_log.c
    * nginx/src/core/ngx_conf.c
    * nginx/src/core/nginx.c重命名为nginx/src/core/webserver.c，并没有修改逻辑？