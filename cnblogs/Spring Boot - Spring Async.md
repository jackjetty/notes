* 除了使用Thread、Runnable、TimerTask、FeatureTask、CompletableFuture等类，在Spring Boot中还可以使用注解创建异步任务，**可以减少线程处理的代码，更简洁更专注于业务逻辑的实现**
* @EnableAsync：指明当前程序后面可以使用@Async注解来管理异步任务
    * 用在用@SpringBootApplication注解的启动类上
    * 用在用@Configuration注解的配置类上
* @Async
    * 加在方法上（**一般是@Service注解的业务类中**）
    * 在被调用时会被异步执行
    * 异步执行只能从外部调用，也就是说，如果是间接被一个非异步的方法内部调用了一个异步方法，那么也会被同步执行
    * 返回值
        * 如果一个异步方法在定义时直接使用了某个类型的返回值，那么其实返回的都是null（如果是基本类型，如果在外面又被用了，那么可能导致装箱，导致有异常，否则不会有异常）
        * Future<T>呢？？