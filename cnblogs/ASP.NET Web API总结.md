#1. 跨域
* 提供Http层的web api时，通常需要考虑跨域问题。
* 因为浏览器处于安全考虑，默认不允许前端页面向不是自己所在的ip/域名发起请求，因此需要服务器端指明自己允许部分或所有域名进行跨域请求
    * 通常是在web.config中配置、web服务器中进行站点配置、根目录下方式跨域文件等方式。
    * 最好不要允许所有域名可以发起跨域请求，可以初步避免XSS，但攻击者其实还是可以通过iframe或jsonp等方式绕过。
* 不能跨域时，从浏览器的返回中可以看到，通常是这个错误：No “Access-Control-Allow-Origin” header is present
* 可以在web.config中增加如下配置以允许跨站请求：
```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="Access-Control-Allow-Origin" value="*" />
      <add name="Access-Control-Allow-Headers" value="Content-Type" />
      <add name="Access-Control-Allow-Methods" value="GET, POST, PUT, DELETE, OPTIONS" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

#2. 405错误
* 有时请求webapi，会返回405 (Method Not Allowed)，可能需要注释掉web.config中的如下配置：
```
<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
      <remove name="OPTIONSVerbHandler" />
      <remove name="TRACEVerbHandler" />
      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
```

#3. 路由配置
* WebApiConfig中的路由配置要避免同一url匹配多个模式或者接口
```
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API 配置和服务

            // Web API 路由
            config.MapHttpAttributeRoutes();

            //config.Routes.MapHttpRoute(
            //    name: "DefaultApi",
            //    routeTemplate: "api/{controller}/{id}",
            //    defaults: new { id = RouteParameter.Optional }
            //);

            config.Routes.MapHttpRoute(
                name: "WebApi",
                routeTemplate: "api/{controller}/{action}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
```

#4. 认证
* 过滤器
```
    /// <summary>
    /// 通用认证过滤器
    /// </summary>
    public class AuthorizeAttribute : ActionFilterAttribute
    {
        /// <summary>
        /// 在调用操作方法之后发生
        /// </summary>
        /// <param name="actionContext">操作执行的上下文</param>
        public override void OnActionExecuting(HttpActionContext actionContext)
        {
            try
            {
                // 记录请求数据

                //string controllName = actionContext.ControllerContext.ControllerDescriptor.ControllerName;
                string actionName = actionContext.ActionDescriptor.ActionName;

                if (actionName != "Login" && actionName != "HasLoggedIn")
                {
                    if (!HttpContext.Current.User.Identity.IsAuthenticated)
                    {
                        actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Forbidden, new ResponseResult<string>() { RetCode = RetCode.Error, Message = CommonMsg.Info_NotLoggedIn });
                    }
                }
            }
            catch (Exception ex)
            {
                Log4netLoggerWrapper.Instance().LogError(ex, $"AuthorizeAttribute|OnActionExecuting--->出错");

                actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Forbidden, new ResponseResult<string>() { RetCode = RetCode.Error, Message = CommonMsg.Error_AuthFail });
                return;
            }

            base.OnActionExecuting(actionContext);
        }

        /// <summary>
        /// 在调用操作方法之后发生
        /// </summary>
        /// <param name="actionExecutedContext">操作执行的上下文</param>
        public override void OnActionExecuted(HttpActionExecutedContext actionExecutedContext)
        {
            // 记录响应数据

            base.OnActionExecuted(actionExecutedContext);
        }
    }
```
* 基本认证