* 自动生成接口文档，并提供接口测试页面

# 1. C#

# 2. Java
* 与.NET版不同，接口、实体类的描述是通过注解去标注的，而不是通过注释（有可能也支持注释的方式？），然后再抽取出这些标注并生成接口文档
* Swagger UI自己的web页面等，直接就在maven引入的jar包中，因此不再需要手动将前端资源拷贝过来，类似Spring boot的jar包启动方式，也包含页面等文件，解压后可以看到。
* 可以查看官方文档，界面可以定制
* 访问地址：http://localhost:8081/swagger-ui.html
* 基本使用
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
            * 接口描述
                * @ApiOperation(value = "获取总体趋势", notes = "获取总体趋势")
                * @ApiOperation("Get test info")
            * 使用@ApiResponses自定义各种Http response的消息体，便于swagger上观察，注意这并不是真实返回的消息体
                * @ApiResponses(value = { @ApiResponse(code = SC_OK, message = "OK", response = List.class) })
* 如果想把Swagger功能做成通用的，那么可以把它做成一个注解
    * 目的
        * 根据配置变量，来动态使用不同的Config设置
        * 这些动态的Config设置中具体包含了host的动态设置、title、description、version和info等的动态设置
    * EnableSwaggerSupport接口定义了注解，具体功能来自SwaggerConfig，使用者只需要在spring boot的启动类上加上这个注解，就有了这个功能
    * SwaggerConfig的具体功能又通过@ComponentScan而扫描包含了来自com.siemens.mindsphere.swaggersupport包下的多个配置类如SwaggerDynamicConfig和SwaggerStaticConfig等？
    * 当前服务用SwaggerDynamicConfig还是SwaggerStaticConfig，是根据当前服务的profile来判断的，SwaggerStaticConfig服务于叫"swagger-static"的profile，而SwaggerDynamicConfig服务于其他的profile
    * 而SwaggerDynamicConfig内部，又根据profile是secured还是local，又会有不同的逻辑，下面的例子中是在profile是secured时会动态指定swagger的host为配置文件中的swagger.host，而不是默认的localhost？
    * 还可以根据当前的profile来使用不同的策略

```
@Retention(value = RetentionPolicy.RUNTIME)
@Target(value = ElementType.TYPE)
@Documented
@Import(SwaggerConfig.class)
public @interface EnableSwaggerSupport {
}


@ComponentScan(basePackages = "com.siemens.mindsphere.swaggersupport")
public class SwaggerConfig {
}


@EnableSwagger2
@Configuration
@Profile("!swagger-static")
@PropertySource("classpath:swagger.properties")
@PropertySource("classpath:application.properties")
public class SwaggerDynamicConfig {

    @Value("${apiInfo.title:Title}")
    private String title;

    @Value("${apiInfo.description:Description}")
    private String description;
    
    @Value("${swagger.host:#{null}}")
    private String host;
    
    @Bean
    @Profile("secured")
    public Docket swaggerDocketConfigCloud() {
        return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.siemens.isb.amp"))
            .paths(PathSelectors.ant("/**"))
            .build()
            .host(host)
            .apiInfo(apiInfo());
    }
    
    @Bean
    @Profile("local")
    public Docket swaggerDocketConfigLocal() {
        return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.siemens.isb.amp"))
            .paths(PathSelectors.ant("/**"))
            .build()
            .apiInfo(apiInfo());
    }

    private ApiInfo apiInfo() {
        ApiInfoBuilder apiInfoBuilder = new ApiInfoBuilder();
        return apiInfoBuilder
            .title(title)
            .description(description)
            .version("1.0")
            .build();
    }
}


@RestController
@Profile("swagger-static")
public class SwaggerStaticConfig {

    private static final String SWAGGER_API_SPEC_JSON_LOCATION = "classpath:swagger-api-spec.json";
    private static final String SWAGGER_API_SPEC_YAML_LOCATION = "classpath:swagger-api-spec.yaml";

    private static final String MEDIA_TYPE_APPLICATION_YAML = "application/yaml";

    @Autowired
    private ResourceLoader resourceLoader;

    @GetMapping(produces = {APPLICATION_JSON_VALUE, MEDIA_TYPE_APPLICATION_YAML}, path = "/v2/api-docs")
    public ResponseEntity<String> swaggerDocs() throws IOException {
        SwaggerSpecResource resource = loadSwaggerSpec();
        String responseBody = CharStreams.toString(new InputStreamReader(resource.getResource().getInputStream(), Charsets.UTF_8));
        return ResponseEntity.ok().header(HttpHeaders.CONTENT_TYPE, resource.getType()).body(responseBody);
    }

    private SwaggerSpecResource loadSwaggerSpec() {

        Resource json = resourceLoader.getResource(SWAGGER_API_SPEC_JSON_LOCATION);
        if (json.exists()) {
            return new SwaggerSpecResource(json, APPLICATION_JSON_VALUE);
        }

        Resource yaml = resourceLoader.getResource(SWAGGER_API_SPEC_YAML_LOCATION);
        if (yaml.exists()) {
            return new SwaggerSpecResource(yaml, MEDIA_TYPE_APPLICATION_YAML);
        }

        throw new SwaggerResourceNotFoundException("Swagger-static profile is enabled, but could not load static swagger specs from classpath.");
    }
}
```

* swagger还可以有一些配置可以放到配置文件中
    * 这些配置是在application.properties中还是自己指定的自定义properties文件中，是SwaggerDynamicConfig的注解上指定的
    * apiInfo.title等有些配置并不是通用的，而是给SwaggerDynamicConfig等中去使用的

```
apiInfo.title=AMP UI Service API
apiInfo.description=Description...
 
loglevelController.setSeverityFilter.notes=Set the severity for a specified logger name.
loglevelController.setSeverityFilter.loggerName.description=The logger name
loglevelController.setSeverityFilter.severity.description=The severity

loglevelController.getSeverityFilter.notes=Get the severity filter for a specified logger name.
loglevelController.getSeverityFilter.logger.description=The name of the logger.

#standard paging parameters
pageable.size.description=The maximum number of elements in a page 
pageable.page.description=The (0-based) index of page
pageable.sort.description=The order of returned elements.<br/>\
Multiple fields could be used separated by commas (e.g. 'field1,field2').<br/>\
Descending order could be requested by appending ',desc' at the end of parameter.(e.g. 'field1,field2,desc')
```