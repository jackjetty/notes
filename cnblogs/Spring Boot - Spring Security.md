* Spring MVC的安全认证
    * 先创建一个继承自WebSecurityConfigurerAdapter的配置类，标识为@Configuration和@EnableWebSecurity注解，进行Spring MVC安全验证设置，默认不做什么自定义配置，即可以在访问接口等资源时返回一个登陆页让输入账号密码（启动时会生成并在日志中输出一个账号密码），也可以自己设置页面和账号密码。
* Restful API的安全认证
    * 通常在前后端分离的情况下，会有一个单独的登陆API甚至登陆Server如Open Auth Server或OpenID Server，由于验证账号密码和返回token，然后在访问业务API时带上token和请求参数
        * 先创建一个继承自WebSecurityConfigurerAdapter的配置类，标识为@Configuration、@EnableWebSecurity和@EnableGlobalMethodSecurity注解，进行**基于Method的Restful API安全验证**设置
            * @EnableGlobalMethodSecurity的prePostEnabled参数用于控制@PreAuthorize、@PostAuthorize、@PreFilter、@PostFilter注解是否生效，前两个可以使用表达式判断用户是否有某些角色或授权
            * @EnableGlobalMethodSecurity的securedEnabled参数用于控制@Secured注解是否生效
            * @EnableGlobalMethodSecurity的jsr250Enabled参数用于控制@RolesRequired注解是否生效
        * 再创建一个获取token的登陆、登出的API，如POST /token
        * 最后创建一个自定义的Filter如AuthenticationTokenFilter，**继承自OncePerRequestFilter，重写doFilterInternal方法，从request的头部获取"Authorization"和"Bearer "字段**），然后进行基于token的身份验证（即根据token获取用户信息），最后把用户认证信息（如UsernamePasswordAuthenticationToken）放到SpringContextHolder中。
* OAuth2.0认证和OpenId
    * OpenId比较老，只实现了用户的追踪，新版也有了认证，但没有授权的功能，目前微信这种应用的第三方认证只是借用了OpenId的概念，并不是一回事
    * OAuth
        * 目前是2.0，与1.0不兼容
        * 密码不需要告诉第三方应用
        * 支持四种授权模式，授权码模式（Authorization Code）最常用，密码模式（Password）一些场景也有使用，其他两种简化模式（Implicit）和客户端模式（Client Credentials）应用较少。
        * Refresh Token不是一种授权模式，但是Spring中可以用于刷新token
        * 流程
            * 可以用postman模拟？
            * 授权码模式
                * 传统方式
                    * OAuth认证完成后通过callback跳回原服务器地址，并同时带上授权码code参数
                    * token只存在于服务器和OAuth服务器间
                    * 客户端只知道授权码，服务器用这个code去OAuth服务器换取token，然后服务器再去OAuth服务器端用token换取用户信息
                * JWT
                    * 自认证的token，使用其中第三部分的签名验证token的有效性（通常用SHA256公私钥不对称加密）
                    * 常用于API访问
                    * 那么服务器和OAuth服务器的两次交互就变成了一次，直接用token换取用户信息
                    * 客户端也直接使用这个自认证的token，而不是授权码code了
                    * 载荷中只做了base64，不要放敏感信息
    * Spring Boot中的OAuth认证实现
        * 主要是要用平台公钥解析请求中的Bearer等token，获取有用信息
        * 在一个@EnableResourceServer标注的config类中配置当前app需要使用OAuth功能（解析token进行认证等）
            * 重写public void configure(final ResourceServerSecurityConfigurer resources)、public void configure(final HttpSecurity http) throws Exception方法，其中使用自己后面实现的类来解析和覆盖设置token信息、用actuator.endpoints.scope控制/manage/的访问？
            * 也可以把这个config和其他config一起做成注解，给其他app使用
        * 实现一个自己的类，用于解析token（因为有时需要使用自己公司或者平台的特殊公钥来进行解密，这个公钥说不定是配在环境变量中的，所以需要一些特殊处理）

```
/**
 * Annotation of enable oauth 2 security support.
 */
@Retention(value = RetentionPolicy.RUNTIME)
@Target(value = ElementType.TYPE)
@Documented
@Import({OAuth2ResourceServerConfiguration.class, WebSecurityConfig.class, OAuth2SecurityConfig.class})
public @interface EnableOAuthSecurity
{
}




/**
 * Configuration of Spring Security OAuth2 for resource server.
 */
@EnableResourceServer
@Configuration
@PropertySource("classpath:actuator-security.properties")
public class OAuth2ResourceServerConfiguration extends ResourceServerConfigurerAdapter
{
    @Autowired
    private ResourceServerTokenServices tokenServices;

    @Value("${uaa.resourceId:#{null}}")
    private String resourceId;

    @Value("#{'${actuator.secured.endpoints}'.split(',')}")
    private List<String> securedActuatorEndpoints;

    @Value("${actuator.endpoints.scope}")
    private String scope;

    @Override
    public void configure(final ResourceServerSecurityConfigurer resources)
    {
        resources.resourceId(resourceId);
        resources.tokenServices(tokenServices);
    }

    @Override
    public void configure(final HttpSecurity http) throws Exception
    {
        for (String securedActuatorEndpoint : securedActuatorEndpoints)
        {
            http.authorizeRequests()
            	.antMatchers("/manage/" + securedActuatorEndpoint)
            	.access("#oauth2.hasScope('" + scope + "')");
        }
        http.authorizeRequests().anyRequest().permitAll();
    }
}


public interface ResourceServerTokenServices {

	/**
	 * Load the credentials for the specified access token.
	 *
	 * @param accessToken The access token value.
	 * @return The authentication for the access token.
	 * @throws AuthenticationException If the access token is expired
	 * @throws InvalidTokenException if the token isn't valid
	 */
	OAuth2Authentication loadAuthentication(String accessToken) throws AuthenticationException, InvalidTokenException;

	/**
	 * Retrieve the full access token details from just the value.
	 * 
	 * @param accessToken the token value
	 * @return the full access token with client id etc.
	 */
	OAuth2AccessToken readAccessToken(String accessToken);

}


/**
 * Handles incoming JWT access tokens issued by UAA server.
 */
@Component
public class RestControllerTokenServices implements ResourceServerTokenServices {
	public static final String ZID = "zid";
    public static final String TEN = "ten";
	public static final String ISS = "iss";
	public static final String SCOPE = "scope";
	public static final String USER_NAME = "user_name";
	public static final String EMAIL = "email";

	public static final String TENANT_NAME_IN_SUBDOMAIN = "tenantNameInSubdomain";
	public static final String TENANT_ID = "tenantId";

	private static final Logger LOG = LoggerFactory.getLogger(RestControllerTokenServices.class);

	private JwtTokenStore tokenStore;

	private List<UaaPublicKeyResource> keyList = new ArrayList<UaaPublicKeyResource>();

	@Autowired
	private OAuth2ConfigurationService config;

	@Autowired
	private PublicKeyProvider publicKeyProvider;

	@PostConstruct
	public void initialize() throws Exception {
		LOG.info(String.format("TokenServices constructed with configuration %s", config));

		keyList = publicKeyProvider.getPublicKeysFromUaa();
	}

	/**
     * Checks token and extracts authentication and further information.
     *
     * The token validity is checked with a public signing key obtained from the
     * UAA server. Further token information is extracted and stored in the
     * SecurityContext to be retrievable in the application.
     */
    @Override
    public OAuth2Authentication loadAuthentication(final String accessToken) throws InvalidTokenException
    {
        try {
			setVerifierKey(accessToken);
		} catch (Exception e) {
			LOG.error("Error while setting verifier key", e);
		}
    	final OAuth2AccessToken token = tokenStore.readAccessToken(accessToken);  
        final OAuth2Authentication auth = tokenStore.readAuthentication(token);
        String tenant = extractTenantIdFromToken(token);
        
        String tenantSubdomain = extractTenantNameInSubdomainFromToken(token);
        auth.setDetails(ImmutableMap.of(TENANT_ID, tenant, TENANT_NAME_IN_SUBDOMAIN, tenantSubdomain));

        LOG.debug("Loaded authentication token for tenant '{}'", tenant);       
        return auth;
    }

	private void setVerifierKey(String token) throws Exception  {
		LOG.info("STEPIN setVerifierKey");

		String publicKeyForXXX = null;
		JwtTokenHeader jwtHeader = JwtTools.extractHeaderFromJwt(token);
		for (int i = 0; i < keyList.size(); i++) {
			if (jwtHeader.getKeyId().equals(keyList.get(i).getKid())) {
				publicKeyForXXX = keyList.get(i).getValue();
				LOG.debug("getKid():" + keyList.get(i).getKid());
				LOG.debug("Public key for current token:" + publicKeyForXXX);
			}
		}
		if(publicKeyForXXX == null)
		{
			publicKeyForXXX = PublicKeyProvider.getDefaultPublicKey();
			LOG.info("Public key was set to default");
		}
		
		// Construct token decoding objects using the signing key
		final JwtAccessTokenConverter tokenConverter = new JwtAccessTokenConverter() {
			@Override
			protected Map<String, Object> decode(final String token) {
				Map<String, Object> decodedToken = super.decode(token);
				decodedToken.put("authorities", decodedToken.get(SCOPE));
				return decodedToken;
			}
		};

		tokenConverter.setVerifierKey(publicKeyForXXX);
		tokenConverter.afterPropertiesSet();
		tokenStore = new JwtTokenStore(tokenConverter);
		LOG.info("STEPOUT setVerifierKey");
	}

	@Override
	public OAuth2AccessToken readAccessToken(final String accessToken) {
		return tokenStore.readAccessToken(accessToken);
	}

	private String extractTenantIdFromToken(final OAuth2AccessToken token) {
        return (String) token.getAdditionalInformation().get(TEN);
	}

	private String extractTenantNameInSubdomainFromToken(final OAuth2AccessToken token) {
		final String uaaUrl = (String) token.getAdditionalInformation().get(ISS);
		return uaaUrl.split("[/-]")[2];
	}
	

}
```