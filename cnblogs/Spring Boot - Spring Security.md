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