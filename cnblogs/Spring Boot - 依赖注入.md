* @Autowired
    * 查找被注解的变量类型，找到所有此类型的构建或此类型子类的构建
    * 如果一个也没有找到，看required参数，false则用null，true则失败（默认，即spring会启动失败）
    * 如果仅找到一个，则装载这个构件
    * 如果找到多个，且只有一个有@Primary注解，使用Primary的
    * 如果不符合上述条件，失败（即spring会启动失败）
    * **如果构造函数的定义在config配置类中，那么必须包一致、返回类型一致、变量名一致，才能对应上，才能自动注入**
* @Qualifier
    * 用于复杂情况下和@Autowired参数一起用，都既可以用于标注在变量上，也可以标注于类定义上
    * 如果属性既有Autowired注解又有Qualifier注解，在构件中查找名字为Qualifier中指定的名字的注解
        * 在构件上指定名字（**项目中要全局唯一**）的两种方法
            * @Service("这里写名字")
            * @Qualifier("这里写名字")
* @Configuration
    * **启动时就会注入？？**
    * @Configuration注解一般注解在这样的类上：这个类里面有@Value注解的成员变量和@Bean注解的方法，就是一个配置类。
    * @Configuration注解是说这个类是一个类似XML文件的配置类，里面用bean标签标记的方法需要被实例化到IOC容器中。在springboot中取消了XML文件并大量使用@Configuration注解的类实现配置。
* @Component
    * 必须用此注解标注了，才能其他地方用@Autowired等方式进行注入
    * @Component注解的范围最广，所有类都可以注解
    * @Component注解是说这个类需要被组件扫描器扫描到并实例化对象到IOC容器
* 注意
    * spring中凡是用@Service等注解标注的都是spring中的构件（Component？），可以使用注入
    * **可以使用@Autowired等方式注入的，必须是@Service、Component等标注的构件！**