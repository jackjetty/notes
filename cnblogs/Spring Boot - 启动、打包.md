* Application.java启动类
    * 用@SpringBootApplication标识Application.java启动类
        * @SpringBootApplication相当于@Configuration + @EnableAutoConfiguration + @ComponentScan
            * @Configuration: Tags the class as a source of bean definitions for the application context.
            * @EnableAutoConfiguration: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if spring-webmvc is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a DispatcherServlet.
            * @ComponentScan: Tells Spring to look for other components, configurations, and services in the hello package, letting it find the controllers.
        * 可以通过scanBasePackages指定要扫描的包，相当于@ComponentScan，如@SpringBootApplication(scanBasePackages = {"com.yyy", "com.xxx" })
    * 其实最终使用了Spring Boot提供的启动方法：SpringApplication.run(Application.class, args);
* 打包运行
    * 可以使用Gradle、Maven来运行、打包程序
    * 默认打包成可执行的jar包，并包含一个内嵌的servlet容器 - Tomcat作为Http runtime。
    * jar包中的Main函数就是前面用@SpringBootApplication标识的Application.java启动类
    * 运行jar包：java -jar xxx.jar