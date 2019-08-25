* 简介
    * 一个jdk自带的内存、性能分析工具，也可以做详细的Cpu、Memory的profiling
    * 在jdk的安装目录下就有，如C:\Program Files\Java\jdk1.8.0_201\bin
    * 可以另外安装一些插件
* 连接
    * 可以远程连服务器上的java程序，进行Profiling
    * 可以像使用localhost那样，同样连接并监控分析ssh tunnel映射到本地的端口（新建一个localhost:5000的JMX Connection）
    * 目标必须是5000端口！？
* Profiling
    * 选中左侧树上的某一个connection，由此会出现其相关的界面（有一些信息可能需要等一下，比如Sampler中的信息及按钮可点击状态）
    * 稍等一下后，可以开始/结束CPU、Memory的profiling
    * 可以在任意时间点生成snapshot，snapshot可以保存和导入
        * ![VisualVM_profiling_create_and_load_snapshot](https://img2018.cnblogs.com/blog/106125/201905/106125-20190524144849248-73459771.png)
        * ![VisualVM_profiling_save_snapshot](https://img2018.cnblogs.com/blog/106125/201905/106125-20190524144904505-970038691.png)
    * 在生成的snapshot中，如cpu profiling可以按Methods、classes、packages查看**函数调用树和时间占用（ms和百分比）**
        * 要关注自己的逻辑代码，而像Tomcat或者RabbitMQ的消费连接，当然都是一直都存在的，所有CPU时间都是满的