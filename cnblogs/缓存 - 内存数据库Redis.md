* 客户端
    * Redis Desktop Manager
        * [官网](https://redisdesktop.com/)
        * 新版的收费，或者要分享什么的。0.8.8的旧版才免费？：https://github.com/uglide/RedisDesktopManager/releases/tag/0.8.8
        * Linux的免费
        * 也可以使用redis-cli命令行工具，在连接上右击选择Console
    * Redis Studio
        * https://github.com/cinience/RedisStudio/releases
* 命令行
    * 可以使用redis-cli命令行工具，是redis安装后带的一个工具，类似的还有redis-server等
    * [Redis 命令参考 ](http://redisdoc.com/configure/index.html)
* 使用
    * 有db0到db15共16个Database，Database下面可以有多个Namespace（可以嵌套），每个Namespace下面可以有好多Key。
    * 每个Key可以设置过期时间TTL
    * **如果有密码，登录时只需要提供密码即可，不需要用户名**
* 使用方式
    * standalone
    * replication
    * 集群
    * 哨兵
* Key的设计
    * 用:分隔
    * 按业务来设计
* 集成spring boot
    * 可参考[Sprint Boot - Spring Cache](https://i.cnblogs.com/EditPosts.aspx?postid=10696423)