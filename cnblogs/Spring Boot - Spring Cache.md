* 缓存
    * 服务器自身（内存）的缓存
        * 利用java程序中的变量
            * 简单
            * 集群环境中多个实例无法共享同步
    * 缓存服务器（一般支持集群、分布式）
        * Redis
        * Memcached
* Spring中使用注解使用缓存
    * 启动类或者配置类
        * 使用@EnableCaching，必须使用，否则后续注解虽然不报错，但没有使用缓存
        * 可以放一个@Bean，用来注入CacheManager

```
@EnableCaching
@EnableScheduling
@Configuration
public class CacheConfig {
	
	@Bean
	@Primary
	public CacheManager cacheManager() {
		return new ConcurrentMapCacheManager("assets");
	}

}
```

    * 使用注解标识方法（）
        * @Cacheable，通常用在get方法上
        * @CachePut：运行后会更新/新建缓存中的值，通常用在update方法上
        * @CacheEvict：运行后会删掉缓存中的值，通常用在delete方法上
    * 使用注解标识类（方法上就不用标识了）
        * @CacheConfig：相当于该类下所有的方法的cacheNames都是其中指定的字符串？
    * 参数
        * 标识缓存项，都是用注解中的cacheNames参数和key参数组合起来的：**缓存的key是cacheNames::key**
            * cacheNames可以是一个字符串数组，可以对应多个缓存的key，每个方法都需要指定cacheNames参数
            * key
                * 通常用SpringEL表达式。
                * 以\#开头，内部以.进行分级分隔。
                * **默认按照方法的参数自动生成**
                * 可以通过keyGenerator参数自定义生成方式
                * 有时key可以省略，key有默认的机制。如果方法只有一个参数，那么就会用cacheNames和参数值组合成key；如果有多个参数的时候，就会把多个参数组合起来再组合成key。**所有要看情况，key有时可以省略，有时不可以。**
        * condition可以指定一个条件，用于执行方法前判断是否使用注解的功能
        * unless用于执行方法后，用返回值？判断是否使用注解提供的功能
        * cacheManager、CacheResolver、keyGenerator
        * @CacheEvict多了两个参数
            * allEntries：值为true则不管key，清除cacheName下所有key的缓存
            * beforeInvocation：值为true，执行方法前清除，默认是执行方法后清除
    * 设置缓存过期
        * 可以和Scheduled Tasks结合，使用CacheEvict注解定时清除缓存
        * 某些缓存服务如Redis，可以通过application.yml配置一个全局的过期时间（默认不过期）
* 使用Redis
    * 在maven或者gradle中加入依赖
    * 在application.yml中加入配置
    * 可以通过使用RedisTemplate（RabbitMQ中有类似的概念用来发数据到队列）单独设置一些过期时间等
    * 有可能的坑
        * application.yml中其实可以配置key前缀、是否使用key前缀。如果打开了这两个开关，那么有可能导致代码中的cacheNames参数失效，如果两个方法的key相同，那么会导致缓存的key相同导致混乱。根本原因是spring把cacheNames参数也当成了application.yml中所谓的key，**所以这两个参数一般不要配**。
    * 使用
        * 需要在build.gradle中添加依赖：compile('org.springframework.boot:spring-boot-starter-data-redis')，然后就可以使用RedisTemplate<String, XXX>类了
        * application.properties文件中的配置
            * spring.redis.database=3
            * spring.redis.host=10.3.50.182
            * spring.redis.port=27000
            * spring.redis.password=RedisPass
            * spring.redis.jedis.pool.max-wait=3600
            * spring.redis.jedis.pool.max-active=1
            * spring.redis.jedis.pool.max-idle=1
            * spring.redis.jedis.pool.min-idle=1
            * spring.redis.timeout=3600
        * 使用RedisConnectionFactory
            * 支持单机、哨兵、集群模式
            * 配置类（其实这一个就够了）
                * 注入生成的redisSetRepository可以直接用来进行redis操作，比如redisRepository.put

```
@Configuration
public class RedisConfig {

    @Autowired
    private RedisConnectionFactory redisConnectionFactory;
    
	@Value("${oee.cache.ttl.hours:24}")
	private long timeout;
   
    @Bean
    public RedisRepository redisRepository()
    {
        return new RedisRepository(cacheEntryRedisTemplate(), timeout, TimeUnit.HOURS);
    }
    
    @Bean
    public RedisSetRepository redisSetRepository()
    {
        return new RedisSetRepository(stringRedisTemplate());
    }

    private RedisTemplate<String, CacheEntry> cacheEntryRedisTemplate()
    {
        StringRedisSerializer stringSerializer = new StringRedisSerializer(StandardCharsets.UTF_8);
        Jackson2JsonRedisSerializer<CacheEntry> contentSerializer = new Jackson2JsonRedisSerializer<>(CacheEntry.class);
        RedisTemplate<String, CacheEntry> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(stringSerializer);
        template.setHashKeySerializer(stringSerializer);
        template.setHashValueSerializer(contentSerializer);
        template.setValueSerializer(contentSerializer);
        template.setEnableDefaultSerializer(false);
        template.afterPropertiesSet();
        return template;
    }
    
    private RedisTemplate<String, String> stringRedisTemplate()
    {
        StringRedisSerializer stringSerializer = new StringRedisSerializer(StandardCharsets.UTF_8);
        Jackson2JsonRedisSerializer<String> contentSerializer = new Jackson2JsonRedisSerializer<>(String.class);
        RedisTemplate<String, String> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(stringSerializer);
        template.setHashKeySerializer(stringSerializer);
        template.setHashValueSerializer(contentSerializer);
        template.setValueSerializer(contentSerializer);
        template.setEnableDefaultSerializer(false);
        template.afterPropertiesSet();
        return template;
    }
}
```

            * 也可以在上层进一步封装一个RedisRepository，封装和使用RedisTemplate的底层操作，然后在配置类中注入初始化它
```
public class RedisRepository {

	private static final Logger LOG = LoggerFactory.getLogger(RedisRepository.class);

	private RedisTemplate<String, CacheEntry> template;
	private long timeout;
	private TimeUnit unit;
	private boolean expire;

	public RedisRepository(RedisTemplate<String, CacheEntry> template, long timeout, TimeUnit unit) {
		this.template = template;
		this.timeout = timeout;
		this.unit = unit;
		this.expire = true;
	}

	public RedisRepository(RedisTemplate<String, CacheEntry> template) {
		this.template = template;
		this.expire = false;
	}

	public CacheEntry put(String key, CacheEntry cacheEntry) {
		LOG.trace("STEP IN");

		BoundValueOperations<String, CacheEntry> boundValueOps = this.template.boundValueOps(key);
		boundValueOps.set(cacheEntry);
		
		if (expire) {
			boundValueOps.expire(this.timeout, this.unit);
		}

		LOG.trace("STEP OUT");
		return cacheEntry;
	}

	public CacheEntry get(String key) {
		LOG.trace("STEP IN");

		CacheEntry cacheEntry = this.template.boundValueOps(key).get();

		LOG.trace("STEP OUT");
		return cacheEntry;
	}
	
	public Set<String> getKeys(String pattern) {
		return this.template.keys(pattern);
	}

}



@Configuration
public class RedisConfig {

    @Autowired
    private RedisConnectionFactory redisConnectionFactory;
    
	@Value("${oee.cache.ttl.hours:24}")
	private long timeout;
   
    @Bean
    public RedisRepository redisRepository()
    {
        return new RedisRepository(cacheEntryRedisTemplate(), timeout, TimeUnit.HOURS);
    }
    
    @Bean
    public RedisRepository redisWorkPieceRepository()
    {
        return new RedisRepository(cacheEntryRedisTemplate());
    }
    
    private RedisTemplate<String, CacheEntry> cacheEntryRedisTemplate()
    {
        StringRedisSerializer stringSerializer = new StringRedisSerializer(StandardCharsets.UTF_8);
        Jackson2JsonRedisSerializer<CacheEntry> contentSerializer = new Jackson2JsonRedisSerializer<>(CacheEntry.class);
        RedisTemplate<String, CacheEntry> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(stringSerializer);
        template.setHashKeySerializer(stringSerializer);
        template.setHashValueSerializer(contentSerializer);
        template.setValueSerializer(contentSerializer);
        template.setEnableDefaultSerializer(false);
        template.afterPropertiesSet();
        return template;
    }
}
```

            * 也可以再在上层进一步封装一个操作类OeeResourceCache，和一个数据类CacheEntry（包含创建时间）作为value放到redis中，其实是使用前面封装并由Config类注入的RedisRepository类

```
@Configuration
public class OeeResourceCacheConfig {

	@Autowired
	private RedisRepository redisRepository;

	@Autowired
	private RedisSetRepository redisSetRepository;

	@Bean
	public OeeResourceCache oeeResourceCache() {
		return new OeeResourceCache(redisRepository);
	}

	@Bean
	public SynchronizationCache synchronizationCache() {
		return new SynchronizationCache(redisSetRepository);
	}
}


public class OeeResourceCache {

	private static final Logger LOG = LoggerFactory.getLogger(OeeResourceCache.class);

	private static final String OEE_RESOURCE_KEY = "oeeResource";

	private RedisRepository redisRepository;

	public OeeResourceCache(RedisRepository redisRepository) {
		this.redisRepository = redisRepository;
	}

	public void cacheOeeResource(String tenantId, String assetId, OeeResource oeeResource) {

		LOG.debug("Caching OeeResource for tenant {}, assetId {}", tenantId, assetId);
		LOG.trace("OeeResource: {}", oeeResource);

		Check.notNull(tenantId, "tenantId");
		Check.notNull(assetId, "assetId");

		String key = getKey(tenantId, assetId);

		CacheEntry cacheEntry = redisRepository.put(key, CacheEntry.from(oeeResource.toTimeSeriesValue()));

		LOG.info("OeeResource is cached for tenant {}, assetId {}", tenantId, assetId);
		LOG.trace("CacheEntry: {}", cacheEntry);
	}

	public OeeResource getOeeResource(String tenantId, String assetId) {

		LOG.debug("Getting OeeResource from cache for tenant {}, assetId {}", tenantId, assetId);

		Check.notNull(tenantId, "tenantId");
		Check.notNull(assetId, "assetId");

		String key = getKey(tenantId, assetId);

		CacheEntry cacheEntry = redisRepository.get(key);

		LOG.trace("CacheEntry: {}", cacheEntry);

		return cacheEntry == null ? null : OeeResource.from(cacheEntry.to(TimeSeriesValue.class));
	}
	
	public Optional<OeeResource> getOeeResource(String tenantId, String assetId, Instant time) {
		return Optional.ofNullable(getOeeResource(tenantId, assetId)).filter(Objects::nonNull)
				.filter(oeeResource -> oeeResource.getDate().equals(time));
	}
	
	private String getKey(String tenantId, String assetId) {
		return String.join(":", OEE_RESOURCE_KEY, tenantId, assetId);
	}
}


@Getter
@Setter
@ToString
public class CacheEntry {

	private String data;

	@JsonInstant
	private Instant createdAt;

	public static CacheEntry from(Object object) {
		CacheEntry entry = new CacheEntry();
		entry.setCreatedAt(Instant.now());
		entry.setData(Misc.serializeSafeJson(object));
		return entry;
	}
	
	public <T> T to(Class<T> theClass) {
		return Misc.deserializeSafeJson(data, theClass);
	}
}
```

        * 使用redisson库
            * redisson是一个用于连接redis的java客户端工作，相对于jedis，是一个采用异步模型，大量使用netty promise编程的客户端框架
            * 和前面同样的流程，Config类中先注入connection factory，再注入RedisTemplate
            * 同样支持单机、哨兵、集群模式
            * **无法使用cloud foundry的自动注入绑定的service的配置的功能**

```
@Configuration
public class DefaultRedisConfig  {
    @Value("${spring.redis.user.database}")
    private int userDB;
    @Value("${spring.redis.basic.database}")
    private int basicDB;
    @Value("${spring.redis.auction.database}")
    private int auctionDB;
    @Value("${spring.redis.default.database}")
    private int defaultDB;
    @Value("${spring.redis.host}")
    private String host;
    @Value("${spring.redis.port}")
    private int port;
    @Value("${spring.redis.password}")
    private String pwd;


    public JedisConnectionFactory defaultRedisConnectionFactory(int db){
        return getJedisConnectionFactory(db, host, port, pwd);
    }

    private JedisConnectionFactory getJedisConnectionFactory(int dbIndex, String host, int port, String pwd) {
        RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
        redisStandaloneConfiguration.setDatabase(dbIndex);
        redisStandaloneConfiguration.setHostName(host);
        redisStandaloneConfiguration.setPort(port);
        redisStandaloneConfiguration.setPassword(RedisPassword.of(pwd));
        return new JedisConnectionFactory(redisStandaloneConfiguration);
    }

    public RedisTemplate defaultRedisTemplate(int db){
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new FastJsonRedisSerializer(Object.class));
        redisTemplate.setValueSerializer(new FastJsonRedisSerializer(Object.class));
        redisTemplate.setConnectionFactory(defaultRedisConnectionFactory(db));
        return redisTemplate;
    }

    @Bean(name = "userRedisTemplate")
    public RedisTemplate userRedis() {
        return defaultRedisTemplate(userDB);
    }

    @Bean(name = "basicRedisTemplate")
    public RedisTemplate basicRedis() {
        return defaultRedisTemplate(basicDB);
    }

    @Bean(name = "auctionRedisTemplate")
    public RedisTemplate auctionRedis() {
        return defaultRedisTemplate(auctionDB);
    }

    @Bean(name = "redisTemplate")
    public RedisTemplate redis() {
        return defaultRedisTemplate(defaultDB);
    }

}
```