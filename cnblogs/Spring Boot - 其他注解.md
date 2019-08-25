* @PostConstruct和@PreConstruct
    * 从Java EE5规范开始，Servlet增加了两个影响Servlet生命周期的注解（Annotation）。这两个注解被用来修饰一个非静态的void()方法.而且这个方法不能有抛出异常声明。
    * @PostContruct是spring框架的注解，在方法上加该注解会在项目启动的时候执行该方法，也可以理解为在spring容器初始化的时候执行该方法
    * 被@PostConstruct修饰的方法**会在服务器加载Servlet的时候运行，并且只会被服务器调用一次**，类似于Serclet的inti()方法。被@PostConstruct修饰的方法会在构造函数之后，init()方法之前运行。
* @Profile
* @Retention、@Target、@Documented、@Import