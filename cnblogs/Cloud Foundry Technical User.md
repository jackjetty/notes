* 理论
    * 每个tenant都会有一个自己的technical user，包括iot tenant也是一样
    * operator tenant的technical user并没有权限去平台读写iot tenant的数据，因此在schedule task或者接口中，需要用相应的iot tenant的technical user去调平台接口做他自己的事
    * 所有technical user的信息都配置在每个APP的环境变量中，即需要环境变量的tech_info数组中包含这些iot tenant的technical user的信息，会在启动时把他们都加载到CredentialsAccessTokenAcquirer的一个map中，然后后面在使用runWithTechnicalUser方法时会从这个map中找到对应tenant的technical user的信息。
    * TechUserProvider和MultipleTechUserProvider其实是一样的？！尽管调用的AccessTokenAcquirer（CredentialsAccessTokenAcquirer）的acquireAccessToken方法不同？
* 使用
    * 最外层的调用：public <T> T runWithTechnicalUser(String tenant, Func0<T> consumer)
        * 使用这个方法时，如果这个consumer实际运行的方法会调用其他APP或者平台的接口，那么就会把指定tenant的jwt token放到请求头中，那么就相当于是这个tenant的technical user在访问对方的APP接口或平台接口
        * 和这个方法是在接口调用时触发，还是schedule task中触发无关
    * amp的**schedule task**中，会取到所有配置到环境变量中的tenant的technical user，然后循环用每个tenant自己的technical user去处理各自的数据，如刷新各个tenant的assets资源或者计算各个tenant的oee或者发送各个tenant的oee timeseries数据
    * 如果是**接口触发**的处理，那么通常用的就是请求头中的token
        * 但是也有特殊的，比如MMM的SinumerikBasicService需要往平台的billing接口发送数据，就要用到当前用户iot tenant的technical user来发。类似的还有SinumerikAgentCom和SinumerikTimeseriesToIsbSender。
        * 同一个space下的APP建可以直接调用接口，而不同space下的APP间调用需要使用technical user（有什么权限？space？org？）拼出jwt token放到请求中（也需要使用runWithTechnicalUser来把token放到头中）