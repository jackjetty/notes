# 1. 概念
* ![RabbitMQ Routing](https://img2018.cnblogs.com/blog/106125/201904/106125-20190412153353939-46644404.png)
* Broker
    * 可以简单理解为一个RabbitMQ服务器
* Connection
    * 代表一个客户端到RabbitMQ broker的连接
* Channel
    * 为了节省TCP连接资源，一个客户端有多个线程连接到RabbitMQ时，会复用同一个Connection，但会有不同的多个Channel
    * 使用了类似NIO的做法
    * 适用于每个Channel流量不是很大时，否则会在一个Connection中产生性能瓶颈，还是建议开辟多个Connection。
* Exchange
    * 交换器，相当于路由，用于将Channel中的消息绑定到Queue
    * 可以有很多个
    * 一个Exchange可以绑定到多个Queue
    * 有四种Exchange类型，也就是有四种绑定方式/规则
        * 都是用RoutingKey（用哪一个Exchange）和BindingKey（和Exchange Type结合起来判断到哪一个Queue）来判断消息要通过本Exchange路由到哪些队列上
    * 使用前需要把Exchange、Queue及它们的RoutingKey、BindingKey设置好，就像数据库要先建好。
* Queue
    * 最终存储消息的队列，可以有很多个
    * Queue中数据的状态
        * **举个栗子：假如Rabbit 从队列里取出一条消息通过网络发送到消费端需要花费50ms,而消费者处理一条消息需要花费4ms。当消费者处理完成后会发送一条ack消息到Rabbit服务端，同样也花费50ms.这里可以看到，一条完整的消息的处理需要花费104ms，如果我们每次只预取一条（prefetch = 1）,那么在一次消息处理中客户端忙的时间只有4ms,其效率只有4/104 = 3.8%,正常情况下我们希望客户端一直处于忙碌状态。**
        * Ready
            * A message is Ready when it is waiting to be processed.
            * 已经准备好，但还没开始处理的！？
        * **Unacked与Prefetch count**
            * When a consumer connects to the queue it gets a batch of messages to process. The amount is given in the prefetch size. While this consumer is working on the messages they get the status unacked.
            * Unacked means that the consumer has promised to process them but has not acknowledged that they are processed. When the consumer crashed the queue knows which messages are to be delivered again when the consumer comes online. When you have multiple consumers the messages are distributed among them.
            * 已经开始处理了，但还没有反馈说是否已经处理结束。
            * 那么通常来说就是处理中的喽！？
            * **Prefetch count**
                * 和Consumer消费者的**Prefetch count**值有关，指最大处理的数量，如果消息太多，Unacked数量就会慢慢增加至和Prefetch count相等吧？
                * 一个重要却容易被忽略的指标，指单一消费者最多能消费的unacked messages数目。
                * mq为每一个 consumer设置一个缓冲区，大小就是prefetch。每次收到一条消息，MQ会把消息推送到缓存区中，然后再推送给客户端。当收到一个ack消息时(consumer 发出baseack指令)，mq会从缓冲区中空出一个位置，然后加入新的消息。但是这时候如果缓冲区是满的，MQ将进入堵塞状态。
                * 更具体点描述，假设prefetch值设为10，共有两个consumer。也就是说每个consumer每次会从queue中预抓取 10 条消息到本地缓存着等待消费。同时该channel的unacked数变为20。而Rabbit投递的顺序是，先为consumer1投递满10个message，再往consumer2投递10个message。如果这时有新message需要投递，先判断channel的unacked数是否等于20，如果是则不会将消息投递到consumer中，message继续呆在queue中。之后其中consumer对一条消息进行ack，unacked此时等于19，Rabbit就判断哪个consumer的unacked少于10，就投递到哪个consumer中。
                * prefetch的大小应该为多少
                    * 理想状况下，计算MQ SERVER 从缓冲区中拿到消息并推送到消费端，加上消费端处理完ack消息到MQ server,的时间，假设为100ms，其中消费端处理业务话费了10ms。这里可以得出我们 prefetch = 100ms / 10ms = 10，也就是消息来回的总时间/业务处理的时间，这里要求我们 prefetch >= 10。一般计算这个时间不会太准确只能毛姑姑的，所以prefetch一般要大一点。但是这个值也不能太大，不然消费端就一只处于空闲状态了。所以如果你保证所有的消息都ack了，但是还是出现比较长时间的堵塞，你就或者加大一点prefetch，或者多加一些机器，或者减少业务处理的时间了。

# 2. 使用
* 安装时需要同时安装elang
* 安装后在sbin目录执行命令（只第一次安装时执行即可，cmd要以管理员权限打开，如C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.2\sbin）
    * rabbitmq-service.bat remove
    * rabbitmq-service.bat install
    * rabbitmq-service.bat start
    * rabbitmq-plugins enable rabbitmq_management –online
* 管理界面
    * http://localhost:15672/#/，默认账号密码是guest、guest
    * 在Queue界面可以通过get message来获取当前队列中的一个消息进行观察

# 3. 注意
* 消费者进程的业务处理逻辑和接收消息的逻辑不一定需要使用同一个线程，可以存入内存中如用Java中的BlockingQueue，也就是说可以进一步解耦，提高整个应用的处理效率。
* **多个消费者可以订阅同一个队列，这时队列中的消息会被平均分摊（Round-Robin，即轮询），给多个消费者进行处理，而不是每个消费者都收到所有的消息并处理。**
* **RabbitMQ不支持地恶劣层面的广播消费，除非自己二次开发，处理逻辑会变得异常复杂，同时不建议这么做。**
    * 也就是说，**一个队列的消息只能被消费一次！！！最多是可以通过某种方式保证多个消费者能做到轮询。因此如果有多个消费者都各自需要使用一次某数据，那么就要为这个Exchange创建和消费者相同个数的Queue，有数据生成时这几个Queue都要塞进去，然后各个消费者各自消费各自的Queue。**
    * **因此在设计mq结构时，一定要想好这个数据是只要有一个消费者处理一次即可（比如计算统计数据，有人算一次就行，无所谓谁算，也只需要算一次），还是每个消费者都要用到这个数据（比如队列中放的是缓存更新时间，而每个服务器实例都需要消费它来更新自己的内存缓存）？**

# 4. 整合SpringBoot
* 貌似都是符合AMQP标准的操作
* 可以使用spring-rabbit的话
    * **必须和spring boot的版本相同**，否则可能会莫名其妙的错误，如ClassNotFound什么的
    * 可以从maven仓库中引入，repositories中使用mavenCentral()，然后在dependencies中加入compile(group: 'org.springframework.amqp', name: 'spring-rabbit', version: "2.1.4.RELEASE")
* [springboot+rabbitmq例子](https://www.cnblogs.com/skyessay/p/6928933.html)
* 配置连接（也可以和ProducerConfig或者ConsumerConfig合并）

```
@Configuration
public class RabbitMQConfig {
	/**
	 * 注入配置文件属性
	 */
	@Value("${spring.rabbitmq.addresses}")
	String addresses;// MQ地址
	@Value("${spring.rabbitmq.username}")
	String username;// MQ登录名
	@Value("${spring.rabbitmq.password}")
	String password;// MQ登录密码
//	@Value("${spring.rabbitmq.virtual-host}")
//	String vHost;// MQ的虚拟主机名

	/**
	 * 创建 ConnectionFactory
	 *
	 * @return
	 * @throws Exception
	 */
	@Bean
	public ConnectionFactory connectionFactory() throws Exception {
		CachingConnectionFactory factoryBean = new CachingConnectionFactory();
//		factoryBean.setVirtualHost(vHost);
		factoryBean.setAddresses(addresses);
		factoryBean.setUsername(username);
		factoryBean.setPassword(password);
		return factoryBean;
	}

//    /**
//     * 创建 RabbitAdmin
//     *
//     * @param connectionFactory
//     * @return
//     * @throws Exception
//     */
//    @Bean
//    public RabbitAdmin rabbitAdmin(ConnectionFactory connectionFactory) throws Exception {
//        RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
//        return rabbitAdmin;
//    }
}



server.port=8888

spring.rabbitmq.addresses=localhost:5672
#spring.rabbitmq.host=localhost 
#spring.rabbitmq.port=5672 
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
#spring.rabbitmq.virtual-host=/
```

* 生产者
    * **可以在生成者中创建尚不存在的Exchange、Queue、它们之间的绑定关系等。**
        * 下面的例子代码还没试过

```
@Configuration
public class ProducerConfig {
    @Bean
    public RabbitMessagingTemplate msgMessageTemplate(ConnectionFactory connectionFactory) {
        RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
        //参数列表分别是：1.交换器名称(default.topic 为默认值),2.是否长期有效,3.如果服务器在不再使用时自动删除交换器
        TopicExchange exchange = new TopicExchange("default.topic", true, false);
        rabbitAdmin.declareExchange(exchange);
        //1.队列名称,2.声明一个持久队列,3.声明一个独立队列,4.如果服务器在不再使用时自动删除队列
        Queue queue = new Queue("test.demo.send", true, false, false);
        rabbitAdmin.declareQueue(queue);
        //1.queue:绑定的队列,2.exchange:绑定到那个交换器,3.test2.send:绑定的路由名称
        rabbitAdmin.declareBinding(BindingBuilder.bind(queue).to(exchange).with("test2.send"));
        return RabbitUtil.simpleMessageTemplate(connectionFactory);
    }
}
```

    * **使用RabbitTemplate对象来发送消息**
        * **在构建时已指定Exchange名称，发送消息时再指定Routing Key，因为一个RabbitTemplate对应一个Exchange，这个Exchange需要结合Routing Key和Binding Key来决定这条消息会到哪些Queue**
        * 自定义的Template继承自RabbitTemplate，可以设置AfterReceivePostProcessor和BeforePublishPostProcessors，这两者都是MessagePostProcessor对象，重写其postProcessMessage方法可以作为回调函数或者做一些事情。
    * 在@Configuration配置类中
        * 定义在注入时如何初始化systemEventMessageTemplate对象
        * **下面例子中，第二个用于注入的主构造函数中生成了自定义的RabbitTemplate，并指定了Exchange的名称**

```
public class RabbitMQProducerConfig {

	@Bean
	public Exchange simpleExchange() {
		return new TopicExchange(RabbitMQConstants.exchange_name, false, false);
	}

	/**
	 * 初始化 RabbitMessagingTemplate
	 *
	 * @param connectionFactory
	 * @return
	 */
	public static RabbitMessagingTemplate simpleMessageTemplate(ConnectionFactory connectionFactory) {
		RabbitTemplate template = new RabbitTemplate(connectionFactory);
		RabbitMessagingTemplate rabbitMessagingTemplate = new RabbitMessagingTemplate();
//		template.setExchange(RabbitMQConstants.exchange_name);
		rabbitMessagingTemplate.setMessageConverter(new GenericMessageConverter());
		rabbitMessagingTemplate.setRabbitTemplate(template);
		return rabbitMessagingTemplate;
	}
}
```

    * 在@Component组件类（如包含用@Scheduled注解的定时任务中）或者@Service业务处理类中
        * 注入RabbitTemplate类用于发送消息
            * **下面例子中，已经在在注入时用前面的Configuration类中的逻辑指定了Exchange名称**
            * **下面例子中，RabbitTemplate类的convertAndSend方法的第一个是routingKey参数，也就是发送消息除了RabbitTemplate中指定的Exchange名称，还需要Routing Key，才能决定到哪个Queue**

```
	@Autowired
	private RabbitTemplate simpleMessageTemplate;

	simpleMessageTemplate.convertAndSend(RabbitMQConstants.exchange_name, RabbitMQConstants.routing_key, RabbitMQConstants.value);```

* 消费者
    * **可以在消费者中创建尚不存在的Exchange、Queue、它们之间的绑定关系等。消费者的配置中会指定Exchange和Queue的Binding，然后在连接RabbitMQ时就会自动创建Exchange、Queue并绑定。**
        * 为什么？如果没有消费者，只是生产者的话，消息不就推不到Queue中了吗？
    * 也可以像生产者那样使用MessagePostProcessor
    * 直接使用
        * 实现消费逻辑（继承自ChannelAwareMessageListener，然后后面要传给Configuration配置类作为处理逻辑listener）

```
public class RabbitMQChannelAwareMessageListener implements ChannelAwareMessageListener {

	@Override
	public void onMessage(Message message, Channel channel) throws Exception {
		// TODO Auto-generated method stub
	}

}
```

        * 配置
            * valueSetListenerBinding()方法中使用BindingBuilder设置Exchange和Queue之间的Binding Key，其通过@Qualifier注解使用到了本类中注入的valueSetListenerQueue和mtaVariableSetOeePreprocessorExchange。
                * 应该就会自动创建Exchange、Queue及Binding了。
            * mtaValueSetListenerContainer()方法中会构造SimpleMessageListenerContainer，相当于指明使用的Connection、Queue和其他配置信息，这个方法中会创建Exchange、Queue及他们之间的绑定关系。

```
@Configuration
public class RabbitMQConsumerConfig {

	@Autowired
	private ConnectionFactory connectionFactory;

	/**
	 * 作为回调函数处理收到消息，继承自ChannelAwareMessageListener
	 * @return
	 */
    @Autowired
    private RabbitMQChannelAwareMessageListener listener;

	/**
	 * 声明Queue
	 * @return
	 */
	@Bean
    public Queue testListenerQueue()
    {
        return new Queue(RabbitMQConstants.queue_name, false);
    }

	/**
	 * 声明Exchange
	 * @return
	 */
    @Bean
    public TopicExchange testExchange() {
        return new TopicExchange(RabbitMQConstants.exchange_name, false, false);
    }

	/**
	 * 声明Exchange和Queue的绑定关系
	 * @return
	 */
	@Bean
	public Binding valueSetListenerBinding(@Qualifier("testListenerQueue") Queue valueSetListenerQueue,
			@Qualifier("testExchange") TopicExchange mtaVariableSetOeePreprocessorExchange) {
		return BindingBuilder.bind(valueSetListenerQueue).to(mtaVariableSetOeePreprocessorExchange).with("#");
	}

	/**
	 * 创建Exchange、Queue及他们之间的绑定关系。并指定
	 * @return
	 */
    @Bean
    public SimpleMessageListenerContainer mtaValueSetListenerContainer()
    {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        MessageListenerAdapter listenerAdapter = new MessageListenerAdapter(listener);
        container.setMessageListener(listenerAdapter);
        container.setQueueNames(RabbitMQConstants.queue_name);
        container.setAcknowledgeMode(AcknowledgeMode.AUTO);
        container.setPrefetchCount(100);
        
        return container;
    }
}
```

    * 间接把MQ相关的配置和逻辑做成注解，然后再使用，方便复用
        * 定义配置类
            * AssetsCacheConfig类定义了后面会用到的提取出来的不同的逻辑处理，和RabbitMQ相关的是CacheUpdater部分，

```
@Configuration
public class AssetsCacheConfig {

	@Bean
	public AssetsCache assetsCache(UIServiceAccessor uiServiceAccessor) {
		return new AssetsCache(uiServiceAccessor);
	}

	@Bean
	public CacheUpdater cacheUpdater(AssetsCache assetsCache) {
		return new AssetsCacheUpdater(assetsCache);
	}
	
	@Bean
	public AssetStatusChecker assetStatusChecker(AssetsCache assetsCache) {
		return new AssetStatusChecker(assetsCache);
	}
	
	@Bean
	public TimeZoneProvider timeZoneProvider(AssetsCache assetsCache) {
		return new TimeZoneProvider(assetsCache);
	}
}
```

            * AssetsChangedEventReceiverConfig类定义了RabbitMQ的消费配置
                * 和前面直接使用时的配置类类似，也是定义了Binding Key、连接配置等
                * **只有assetsChangedEventReceiver部分提取出来了做成了动态的，前面的AssetsCacheConfig配置类中会注入这个CacheUpdater的实现类，不同的微服务可以通过使用不同的AssetsCacheConfig来使用不同的CacheUpdater的注入实现逻辑**
                * 然后在StartupCacheInitializer组建类中，会在启动时用动态注入的CacheUpdater来初始化缓存

```
@Configuration
public class AssetsChangedEventReceiverConfig {

	@Value("${spring.application.name}")
	private String appName;

	@Bean
	public AssetsChangedEventReceiver assetsChangedEventReceiver(CacheUpdater cacheUpdater) {
		return new AssetsChangedEventReceiver(cacheUpdater);
	}
	
	@Bean
	public Queue assetsChangedEventQueue() {
		return new Queue(getAssetsChangedEventQueueName(), false);
	}

	@Bean
	public TopicExchange systemEventExchange() {
		return new TopicExchange(RabbitConstants.AMP_SYSTEM_EVENT_EXCHANGE, false, false);
	}

	@Bean
	public Binding assetsChangedEventReceiverBinding(@Qualifier("assetsChangedEventQueue") Queue assetsChangedEventQueue,
			@Qualifier("systemEventExchange") TopicExchange systemEventExchange) {
		return BindingBuilder.bind(assetsChangedEventQueue).to(systemEventExchange).with(RabbitConstants.getServiceEventBinding(ServiceEventType.ASSETS_CHANGED));
	}

	@Bean
	public SimpleMessageListenerContainer assetsChangedEventListenerContainer(ConnectionFactory connectionFactory, AssetsChangedEventReceiver assetsChangedEventReceiver) {
		MessageListenerAdapter listenerAdapter = new MessageListenerAdapter(assetsChangedEventReceiver);
		SimpleMessageListenerContainer container = new MtaSimpleMessageListenerContainer();
		container.setConnectionFactory(connectionFactory);
		container.setQueueNames(getAssetsChangedEventQueueName());
		container.setMessageListener(listenerAdapter);
		container.setAcknowledgeMode(AcknowledgeMode.AUTO);
		container.setPrefetchCount(100);

		return container;
	}

	private String getAssetsChangedEventQueueName() {
		return RabbitConstants.getApplicationSpecificSystemEventQueue(ServiceEventType.ASSETS_CHANGED, appName);
	}
}


@Component
public class StartupCacheInitializer {

	private static final Logger LOG = LoggerFactory.getLogger(StartupCacheInitializer.class);
	
	@Autowired
	private CacheUpdater cacheUpdater;
	
	@Autowired
	private TechUserProvider techUserProvider;
	
	@EventListener
	public void init(ApplicationReadyEvent event) {
		LOG.debug("Initializing assets cache");

		try {
			
			techUserProvider.getTenants().forEach(this::update);
			
		} catch (Exception e) {
			LOG.error("Failed to initialize assets cache!", e);
		}
	}

	private void update(String tenantId) {
		LOG.debug("Initializing assets cache for tenant {}", tenantId);
		
		try {
			
			techUserProvider.runWithTechnicalUser(tenantId, () -> cacheUpdater.update(tenantId));
			LOG.info("Assets cache initialized successfully for tenant {}", tenantId);
			
		} catch (Exception e) {
			LOG.error("Failed to initialize assets cache for tenant {}!", tenantId, e);
		}
	}
}
```

        * 定义注解类，引用前面定义的两个配置类

```
@Retention(value = RetentionPolicy.RUNTIME)
@Target(value = ElementType.TYPE)
@Documented
@Import({ 
	CacheManagerConfig.class,
	AssetsCacheConfig.class, 
	AssetsChangedEventReceiverConfig.class
})
public @interface EnableAssetsCaching {

}
```

        * 使用注解：在入口Application类中标注@EnableAssetsCaching

# 5. 参考
* [Rabbit+SpringBoot 指定自动生成exchange、routingkey和Queue对应关系](https://blog.csdn.net/qq_33300570/article/details/82147825)
* [RabbitMQ学习总结](http://www.cnblogs.com/leocook/p/mq_rabbitmq_0.html)