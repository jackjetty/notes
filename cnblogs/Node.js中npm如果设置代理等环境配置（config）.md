* npm获取配置有6种方式，优先级由高到底。
    * 命令行参数。 --proxy http://server:port即将proxy的值设为http://server:port。
    * 环境变量。 以npm_config_为前缀的环境变量将会被认为是npm的配置属性。如设置proxy可以加入这样的环境变量npm_config_proxy=http://server:port。
    * 用户配置文件。可以通过npm config get userconfig查看文件路径。如果是mac系统的话默认路径就是$HOME/.npmrc。
    * 全局配置文件。可以通过npm config get globalconfig查看文件路径。mac系统的默认路径是/usr/local/etc/npmrc。
    * 内置配置文件。安装npm的目录下的npmrc文件。
    * 默认配置。 npm本身有默认配置参数，如果以上5条都没设置，则npm会使用默认配置参数。
* 针对npm配置的命令行操作
    * npm config set <key> <value> [--global]
    * npm config get <key>
    * npm config delete <key>
    * npm config list
    * npm config edit
    * npm get <key>
    * npm set <key> <value> [--global]
* 为npm设置代理
    * $ npm config set proxy http://server:port
    * $ npm config set https-proxy http://server:port
    * 如果代理需要认证的话可以这样来设置。
        * $ npm config set proxy http://username:password@server:port
        * $ npm config set https-proxy http://username:pawword@server:port
    * 如果代理不支持https的话需要修改npm存放package的网站地址。
        * $ npm config set registry "http://registry.npmjs.org/"
* 在设置配置属性时属性值默认是被存储于用户配置文件中，如果加上--global，则被存储在全局配置文件中。
* 如果要查看npm的所有配置属性（包括默认配置），可以使用npm config ls -l。
* 如果要查看npm的各种配置的含义，可以使用npm help config。