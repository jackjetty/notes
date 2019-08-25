* Windows->Preferences->General->Network Connections下面可以设置代理
    * 如果要指定特定的代理地址，需要把类型改为Manual
    * 通常http、https、socks的代理都要一起改
    * Proxy bypass指哪些地址不需要走代理，通常是localhost、127.0.0.1
* 只对Eclipse自身的网络使用有影响，Maven、Gradle的代理设置都在其各自的配置文件中