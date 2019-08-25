#1. 简介
* 目的：简化配置、内嵌Tomcat方便部署（尤其微服务）
* 版本：SNAPSHOT版是快照版，虽然稳定，但还会有更新
* 创建空项目：https://start.spring.io/上创建下载，然后再从文件系统导入到eclipse或者IDEA中
    * web应用需要在pom.xml中增加依赖spring-boot-starter-web
    * 可以输入所需要的依赖包
    * 可以选是maven还是gradle项目
    * 可以选语言
    * 可以选spring boot版本
* Spring Tool Suite的New->Project->Spring Boot->Spring Starter Project可以一步一步的创建Spring Boot项目，最终调的还是https://start.spring.io/得接口，其他IDE也是，虽然可以选不同service，但目前都是https://start.spring.io/。
* Spring Tool Suite中可以打开Boot Dash这个view，集成Cloud Foundry，方便部署。

#2. web项目结构
* 根包
    * 通常放启动类XxxApplication
    * 加注解@SpringBootApplication，也可以同时限定扫描和排除的包@SpringBootApplication(scanBasePackages = { "xxx.xxx" },exclude = {xxx.class})
        * 启动类其实名字可以随便起
    * 还可以加注解@MapperScan("microservice.qssj.mapper")，默认不加的话只扫描main类所在包及子包下面的controller，否则就要加scanBasePackages或@ComponentScan
* controller
    * 接口层
    * 为controller类加注解@RestController和@RequestMapping("xxx")，**xxx区分大小写**
    * 使用到的其他类如Service类使用注解@Resource进行注入
    * 为具体的接口方法增加注解@CrossOrigin、@ApiOperation(value = "xxx", notes = "xxx")、@RequestMapping(value = "xxx", method = RequestMethod.GET)，**xxx区分大小写**
    * 返回值可以自定义，GET方式参数要使用注解@RequestParam(required = true)，POST方式参数要使用注解@RequestBody，这些参数类都要实现Serializable
    * **可以使用ResponseEntity<T>处理Controller的返回值，T为要返回的内容，可以同时指定返回状态码**
        * 如果想避免重复代码，可以抛出异常，然后用@RestControllerAdvice来标识自定义全局异常处理类、用@ExceptionHandler标识方法来做全局的异常处理，统一包装返回给客户端的信息
* service
    * 接口上使用注解@Service("xxxService")或只是@Service
    * 在需要使用的job或controller类中注入时，类型为接口类型即可，不需要具体到实现类
* serviceImpl
    * 类上使用注解@Service("xxxService")或只是@Service
    * 如果没有继承自service接口，在需要使用的job或controller类中注入时，需要具体到实现类
    * 使用到的其他类如Mybatis的Mapper类使用注解@Resource进行注入
    * 可以为成员使用注解@Value("${xxx}")为其赋上properties文件中的配置值
* db.mapper.primary
    * 定义访问数据的方法，可以直接用sql语句，也可以指定provider类中的方法拼接sql，也可以使用xml文件

```
@Mapper
public interface EUserInfoMapper {

	 /**
     *用户登录
     * @param userName
     * @param pwd 
     * @return
     */
    @Select("select * from  `staff` where id=#{userName} and password=#{pwd}")
    EUser login(@Param("userName") String userName,@Param("pwd") String pwd);
    
     * @return
     */
    @SelectProvider(type = UserSqlProvider.class, method = "getUsersFilter")
    List<EUser> serachUsers(SerachUserVO serachUserVO);
   
}

```

* db.mapper.provider
    * 相当于动态拼接sql语句，也类似于传统的mybatis的xml文件的作用

```


public class UserSqlProvider {

    public String getUsersFilter(final SerachUserVO serachUserVO) {
        String sql =new SQL() {
            {
                SELECT("*");
                FROM("staff");
                WHERE("del_flag=0");
                if (StringUtil.isNotEmpty(serachUserVO.getUser_name())) {
                    WHERE("id=#{user_name}");
                }
                if (StringUtil.isNotEmpty(serachUserVO.getReal_name())) {
                	WHERE("name=#{real_name}");
                }
                if (StringUtil.isNotEmpty(serachUserVO.getMobile())) {
                	WHERE("cell_phone=#{mobile}");
                }
                ORDER_BY("id desc limit #{page_index},#{page_size}");
            }
        }.toString();
        return sql;
    }
}
```

* exception
* entity
    * 最好还是按用途分层使用：vo、po等
    * 如果是用于mybatis数据访问的实体类，比如primary包中返回的类型，那么每个字段名称都要和数据库中一致
* util
* config
    * MybatisDbConfig.java

```
@Configuration
@MapperScan(basePackages = "xxx.db.mapper.primary", sqlSessionTemplateRef = "primarySqlSessionTemplate")
public class MybatisDbAConfig {

    @Value("${pagehelper.offsetAsPageNum}")
    private String offsetAsPageNum;

    @Value("${pagehelper.helperDialect}")
    private String helperDialect;

    @Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    @Primary
    public DataSource primaryDataSource() {
        return new DruidDataSource();
    }

    @Bean(name = "primaryTransactionManager")
    @Primary
    public DataSourceTransactionManager setTransactionManager(@Qualifier("primaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    @Bean(name = "primarySqlSessionFactory")
    @Primary
    public SqlSessionFactory setSqlSessionFactory(@Qualifier("primaryDataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
      //  bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:mapper/base/*.xml"));
        // 分页拦截器-begin
        PageInterceptor interceptor = new PageInterceptor();
        Properties properties = new Properties();
        properties.setProperty("helperDialect", helperDialect);
        properties.setProperty("offsetAsPageNum", offsetAsPageNum);
        interceptor.setProperties(properties);
        bean.getObject().getConfiguration().addInterceptor(interceptor);
        // 分页拦截器-end
        return bean.getObject();
    }

    @Bean(name = "primarySqlSessionTemplate")
    @Primary
    public SqlSessionTemplate setSqlSessionTemplate(@Qualifier("primarySqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}
```

    * DruidDataSourceConfiguration.jva

```
@Configuration
public class DruidDataSourceConfiguration {

	@Bean(name="primaryDataSource")
    @Qualifier("primaryDataSource")
    @Primary
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    public DataSource primaryDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();  
        return druidDataSource;
    }

}
```

    * Swagger2Config.java
* 定时任务job（不需要调用，启动后就开始）
    * 主类上要加@EnableScheduling
    * 使用@Component注解？
    * 使用@Scheduled(cron = "0/10 * * * * ?")来控制间隔时间

#3. 配置文件
* 按约定，spring的配置文件是src/main/resources/application.properties（IDEA用的是application.yml）
    * 根据application.properties中的spring.profiles.active=test配置，找到当前环境对应的真正的配置文件，如application-test.properties
    * 可通过server.port修改端口号
* kafka的配置文件可以是producer.properties和consumer.properties，然后程序中读取并使用
* 可以全局指定时区的日期转json字符串的方式，时间戳一般专指一个格式

#4. Swagger
* 与.NET版不同，接口、实体类的描述是通过注解去标注的，而不是通过注释（有可能也支持注释的方式？），然后再抽取出这些标注并生成接口文档
* Swagger UI自己的web页面等，可能直接就在maven引入的jar包中，类似Spring boot的jar包启动方式，也包含页面等文件，解压后可以看到
* 访问地址：http://localhost:8081/swagger-ui.html
* 安装
    * pom.xml文件增加依赖并maven update project

```
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.2.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.2.2</version>
        </dependency>
```

    * 在单独的配置文件中配置接口包地址、生成的接口的版本描述名称

```
@Configuration
@EnableSwagger2
public class Swagger2Config {

	@Bean
	public Docket createRestApi() {
		return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo()).select()
				.apis(RequestHandlerSelectors.basePackage("com.eastmoney.articlemanager.controller")).paths(PathSelectors.any()).build();
	}

	private ApiInfo apiInfo() {
		return new ApiInfoBuilder().title("xxx RESTful APIs")
				.description("xxx")
				 .version("1.0").build();
	}

}
```

    * 在需要提供描述信息的位置上增加注解
        * Model类的属性注解

```
public class BaseResponse {
	@ApiModelProperty("返回编码")
	private String resultCode;

	@ApiModelProperty("返回信息")
	private String resultMsg;

	@ApiModelProperty("错误码")
	private String errorCode;

	@ApiModelProperty("返回具体内容")
	private Object data;

	public void setResult(String resultCode, String resultMsg, String errorCode, Object data) {
		this.resultCode = resultCode;
		this.resultMsg = resultMsg;
		this.errorCode = errorCode;
		this.data = data;
	}

	public BaseResponse() {

	}

}
```

        * 接口类的注解
            * @ApiOperation(value = "获取总体趋势", notes = "获取总体趋势")

#5. lombok
* 安装
    * 先要在pom.xml文件中增加依赖（可以不指定版本，eclipse会自动选择）

```
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```

    * 然后在下载的lombok.jar依赖包目录运行java -jar lombok.jar以打开安装界面，选择eclipse安装目录后安装即可
    * 安装完成后要重新maven update project。
* 如果报错“getId() is undefined”，可能是虽然增加了依赖，但没有安装lombok.jar，即参考上一项安装。也有人说是eclipse太新，也有人说需要打开eclipse的注解开关什么的。
* 使用时，需要在类或者成员上标志@Data、@Getter、@Setter

#6. 运行/部署
* 运行
    * Run as或者Debug as为Java Application，在弹窗中需要新建一项，选择Main class然后运行/调试
* 部署
    * 传统、简单的jar方式
        * 内置tomcat（貌似可以换成Jetty等）
        * 生成：使用命令mvn install或者Eclipse上项目右击选择Run As选择maven install（有时生成不了，可能是文件编码等原因报错了，可以命令行跑一下mvn package试一下，看看输出）
        * 部署：target目录会生成一个jar包及相关文件（不知道是不是必要的），直接在该目录java -jar xxx.jar运行即可（在命令行中可通过ctrl+c停止运行）
    * 部署到外部Tomcat中
        * 注意tomcat版本（jdk1.8最好用tomcat1.8）

#7. 项目结构划分
* PBF（按功能划分包） vs PBL（按层次划分包）
* 按层次（都用@Component）
    * Web控制层，常使用@RestController、@Controller
    * 业务逻辑层，常使用@Service
    * 数据访问层（**常使用@Repository**，可以使用Mybatis也可以使用较老的JPA也就是Hibernate）
* 如果较复杂，可以考虑先按功能分，内部再按层次分