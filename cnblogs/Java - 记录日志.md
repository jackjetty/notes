* 比自己写文本日志的好处
    * 默认定义好了一些日志级别，会记录当前使用的级别以上的日志，通常线上环境设置的级别较高记得较少
    * 有一些自动split之类的功能
* Commons-logging
    * 日志级别：TRACE<DEBUG<INFO<WARN<ERROR<FATAL
    * 使用application.yml配置文件
    * 自己的类中：private static final Log log = LogFactory.getLog(Xxxx.class);
    * 可以只有打开Trace时才打出trace信息，省一点计算机资源：if(log.isTraceEnable()){log.trace("1");}
* SLF4j
    * 日志级别：DEBUG<INFO<WARN<ERROR
    * 使用application.yml配置文件
    * 自己的类中：private static final Logger logger = LogFactory.getLogger(Xxxx.class);