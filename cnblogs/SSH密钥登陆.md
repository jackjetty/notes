# 参考：
* [SSH公钥登录原理](https://www.cnblogs.com/scofi/p/6617394.html)

* 比如git可以生成公钥，然后用有权限的账户把他加到仓库上，以后就可以通过公钥登陆了。不需要像https那样需要有账号，但是权限管理就不细了。
    * 有时候如果仓库上添加了多个公钥，会是旧的生效，那么就可能会导致git连不上，需要删掉旧的。为什么？按理说可以多个啊，难道因为多个机器的git的邮箱或者用户名设置的不一样？
    * ssh key只和账号有关系，和设备、连哪个库都没关系？