# 1. MindSphere
* [官网](https://siemens.mindsphere.io)
* [AWS MindSphere Launchpad](https://sncrdsw.eu1.mindsphere.io/index.html#/)
* [Aliyun MindSphere Launchpad](https://mcapps.cn1-pre.mindsphere-in.cn/index.html#/)
* Get started to run a cf app
    * [MindSphere - Get started to run a cf app](https://developer.mindsphere.io/howto/howto-cf-running-app.html)
    * cf cli
        * create account and assign role
        * login
        * create space
        * switch to space
        * 提前准备好jar包等部署包，准备好yml文件，并切换到该目录
        * cf push
    * configure in MindSphere
        * developer cockpit
            * create app and add cloud foundry mapping (app roles will be created automatically and fixed)
            * **create at least one scope this app such as xxx.all？？**
            * register app at the MindSphere Gateway(AWS上每次都需要再保存一次，才能点注册按钮。。。bug!?)
        * assign app admin or user role to user
    * 测试地址：https://sncrdsw-ampuiservice-sncrdsw.eu1.mindsphere.io/
* MindSphere api
    * 如何调用接口：https://developer.mindsphere.io/concepts/concept-authentication.html
    * 获取token：https://developer.mindsphere.io/howto/howto-agent-access-token.html#token-request
    * **想用postman请求MindSphere api接口，只要放到Bearer中的token的user的APP role有相应的api的scope/role就行，不管是technical user的token还是cloud foundry日志中捞的用户的token都可以，注意超时了要更新一下token。。。。**
    * **获取technical user的token（也会超时），把id+secret的base64编码放到postman的Basic认证中，去请求获取Token，后台代码中也是这么干的**：[Self-Hosted Application – Access MindSphere APIs](https://developer.mindsphere.io/howto/howto-selfhosted-api-access.html)
    * APIs & Services文档上有各个api的swagger的地址(**前端调用使用的地址？？**)
        * 如https://developer.mindsphere.io/apis/advanced-assetmanagement/api-assetmanagement-api-swagger-3-9-0.html
        * 如https://{tenantName}-assetmanagement.eu1.mindsphere.io/api/assetmanagement/v3/swagger-ui.html,https://sncrdsw-assetmanagement.eu1.mindsphere.io/api/assetmanagement/v3/swagger-ui.html
    * 从前端访问：{webAppHost}/api/...，如https://sncrdsw-assetmanagement.eu1.mindsphere.io/api/assetmanagement/v3/assets
    * 从后端（**如Cloud Foundry**）通过Gateway访问：{region}.mindsphere.io/api/...，如https://gateway.eu1.mindsphere.io/api/assetmanagement/v3/assets?size=100
* 使用Postman访问MindSphere接口
    * Settings中关掉SSL certificate verification
    * 设置代理为本地cntlm走公司代理
    * Authorizations设成Bearer认证，同时token的值设为Cloud Foundry中从APP的debug日志中抓到的当前有效的token
    * Accept和Content-Type设为application/json
    * Body设为raw - JSON(application/json)
* 授权
    * launchpad里的settings页面只能给user配APP role，其他都是默认的core-role；要在developer Cockpit中给APP的admin、user role配置其他core-role
    * 使用OAuth2认证，基于scope和role
        * application-specific scope就是在Developer Cockpit上面添加的，用于APP从MindSphere透传过来的头中拿到后进行用户授权判断
        * application-specific role在MindSphere中是固定的两个：APP_NAME.admin、APP_NAME.user
        * 其他的mdsp开头的都是MindSphere定义好的core-role和core-scope
    * 从前端到MindSphere Gateway再到APP service的请求，token已经在cookie中了，还提供了x-xsrf-token
    * 从后端直接请求的MindSphere api，需要提供Authorization: Bearer "Token"，Accept和Content-Type设为application/json

# 2. Cloud Foundry
* [文档](https://docs.cloudfoundry.org/)
    * 分为了General Information、Information for Operators、Information for Developers等部分
    * [Concepts](https://docs.cloudfoundry.org/concepts/overview.html)
    * [**Developer Guide**](https://docs.cloudfoundry.org/devguide/)
    * [**命令行索引Cloud Foundry CLI Reference Guide**](http://cli.cloudfoundry.org/en-US/cf/)
* [AWS的CF地址](https://api.cf.eu1.mindsphere.io)
* [Aliyun的CF地址](https://api.cf.cn1.mindsphere-in.cn)
* 与MindSphere的关系
    * 理论上是平级的，都是PaaS层
    * Cloud Foundry专注于在IaaS的基础上，额外提供应用的Middleware、Runtime，以及减少在扩展应用、配置基础设施如负载均衡、灾备等时花费的工作量
* 概念
    * org包含多个space，space包含多个application/service
        * 通常登录后，如果当前用户属于某个org，会自动切换到该org，然后列出所有space让你选择当前要使用的space环境
    * org中用户的角色
        * https://docs.cloudfoundry.org/concepts/roles.html
        * **OrgManager**：managers or other users who need to administer the org
        * **BillingManager**：create and manage billing account and payment information
        * **OrgAuditor**：view but cannot edit user information and org quota usage information
        * **OrgUsers**（命令行中貌似没有列出？）： can view the list of other org users and their roles. When an Org Manager gives a person an Org or Space role, that person automatically receives Org User status in that Org
        * **SpaceMangers**：are managers or other users who administer a space within an org
        * **SpaceDevelopers**：application developers or other users who manage applications and services in a space
        * **SpaceAuditors**：view but cannot edit the space
    * Quota plan
        * https://docs.cloudfoundry.org/adminguide/quota-plans.html
        * 既可以设置org级的quota plan（必须指定一个），也可以设置space级的quota plan（可以不指定）
            * org级的quota plan：每个org可以定义一系列的quota plan，但每次只可以指定一个quota plan到一个org
        * 可以在命令行中创建和使用quota plan，也可以通过manifest.yml在上传APP时同时设置quota plan？？？和yml文件到底有关系吗？？貌似yml文件只是一个app的？？？另外还有一个有quota_definitions的yml文件？？？
    * Service
        * Cloud Foundry提供的中间件
        * 也有user provided service，指用户自己搭建提供的中间件
        * 要先创建service instance，然后绑定到APP
    * 访问相关
        * 一般不用改？
        * **cloud foundry的地址默认外面不能用（返回403）？是不是之前说的单独开通unsecure就是指让这些cloud foundry URL可以外部访问？？**
        * **MindSphere的Developer Cockpit页面配的Components中，是不是每一个Component就是指一个cloud foundry的APP（也可能是api类型）？**
            * **每个component中配的Name就是（必须是）cf apps看到的APP的cloud foundry名称，也是yml文件中配的。**
            * **每个component中配的Cloud Foundry Direct URL就是（必须是）cf apps看到的APP的cloud foundry内部地址。**
            * **每个component中配的Endpoints就是**
        * Domain
            * domain是属于org的
            * domain分http和tcp的
            * **Domain就是定义好的一些地址，但是通常是正常http的URL的后面一段，比如apps.eu1.mindsphere.io。**
            * domain体现为cloud foundry的APP的URL中的后面部分，如apps.eu1.mindsphere.io，其实就是user可以使用什么样的URL？？？
            * 每个org可以有多个domain，默认有一个shared的domain
        * Route
            * route是属于space的，只能绑定同一个space下的route和APP，但是route又是全局唯一的
            * route是基于domain的
            * route分internal和external的，要看基于的是internal还是external的domain，如果是internal的，APP间的请求就不会走外网而是走内部的container network
            * route分http和tcp的
            * Route就是预定义的一些URL地址，访问APP用的完整http URL会与预定义的route进行匹配，匹配成功后请求就被转到了这个route绑定的APP
            * 一个APP可以有多个instance，route绑定到的是APP，会自动做负载均衡到各个instance（round-robin算法）。开发者也可以一个route绑定多个APP，那么负载均衡就包含了所有APP的所有instance。也可以一个APP绑定到多个route，那么这个APP就有多种URL可以访问了。
            * Route是要和APP绑定的。使用cf map-route命令可以把app和route绑定起来
            * **一个请求map到了多个route该怎么办？？**
            * MindSphere上的每个app都有一个自己的前端MindSphere地址，同时也有一个Cloud Foundry的内部地址（即使是MindSphere前端不可见的service）
            * **其实在MindSphere界面的Developer Cockpit中的Components下面增加app、cloud foundry direct url的映射，就是在进行绑定MindSphere app和Cloud Foundry应用。cloud foundry的url可以在cli中通过命令cf apps查到。并且必须增加至少一个Endpoints，使用/\*\*来匹配所有URL**
    * Buildpacks
        * 通常是各个语言的运行环境
    * Cloud Foundry也提供了api接口进行操作：http://apidocs.cloudfoundry.org/7.1.0/
* BOSH
    * https://bosh.io/docs/
* 命令行
    * 先安装文档官网首页或者MindSphere开发者网站的Cloud Foundry下的Resource页面(最终也是跳转的Cloud Foundry文档官网)提供的cli，然后就可以在cmd或者powershell中使用了
    * 文档
        * [**MindSphere HowTos - add and assign user**](https://developer.mindsphere.io/paas/paas-cloudfoundry-howtos.html)
        * 官方文档网站
        * [**命令行索引Cloud Foundry CLI Reference Guide**](http://cli.cloudfoundry.org/en-US/cf/)
        * $ cf help [-a]可以看到这些命令和分类
    * 命令/功能分类
        * Getting started
        * Application(APPS) lifecycle
        * Services integration
        * Route and domain management
        * Space management
            * list, create, delete, rename
            * ssh related
        * Org management
            * list, create, delete, rename
            * quota
            * others
        * Buildpacks management
        * User management
        * Network policies
        * CLI plugin management
        * Others
    * 常用命令
        * 套路
            * 查询某个东西，通常是cf xxxs，比如cf apps、cf routes等等
            * 设置、创建某个东西，通常是cf set-xxx、cf unset-xxx、cf create-xxx
            * 对APP的操作通常是cf xxx APP_NAME
        * 登录
            * $ cf **login** [-a API_URL] [-u USERNAME] [-p PASSWORD] [-o ORG] [-s SPACE]
                * 常用的是cf login -a https://xxx –sso
                    * -sso或者--sso指Prompt for a one-time passcode to login，单点登陆，也就是说使用MindSphere账号登陆Cloud Foundry
                    * 不加-sso的话，需要输入邮箱密码等（**但Cloud Foundry的密码我们并不知道，有办法知道吗？不然build pipeline只能用selenium去抓code了，是不是MindSphere只允许用MindSphere账号单点登录！？**）
                * 过程
                    * 会返回一个让你输入Temporary Authentication Code的信息，并提供一个连接
                    * 在浏览器中打开这个连接并复制提供的code（win7可能要右击选mark再选中连接、右击选择复制）：需要在浏览器中登录，连接中其实没有用户信息，是登录才有的
                    * 在命令中右击（看不到，但实际上已经粘贴上去了，win7可能要右击选中粘贴）并回车
        * org管理及管理用户角色
            * $ cf orgs
                * 列出当前用户所属的org
            * $ cf org ORG [--guid]：查看的信息中包含当前包含哪些space、当前的domains、当前使用的org级quota plan
            * $ cf org-users {orgName}
                * 列出某个org下面所有的用户
            * $ cf set-org-role {userName} {orgName} {role}
            * $ cf unset-org-role {userName} {orgName} {role}
        * space管理及管理用户角色
            * $ cf spaces
                * **执行之前必须先指定当前环境的org，如：cf target -o sncrdsw**
                * **如果没有及时在登录后选择space，可以通过命令cf target -o ORG -s SPACE切换到space**
            * $ cf create-space SPACE [-o ORG] [-q SPACE_QUOTA]
            * $ cf space-users ORG SPACE
            * $ cf set-space-role USERNAME ORG SPACE ROLE
            * $ cf unset-space-role USERNAME ORG SPACE ROLE
        * Quota plan相关
            * org级的quota plan
                * $ cf **quotas**：List available usage quotas 
                * $ cf quota QUOTA：Show quota info 
                * $ cf set-quota ORG QUOTA： Assign a quota to an org 
                * $ cf **create-quota** QUOTA [-m TOTAL_MEMORY] [-i INSTANCE_MEMORY] [-r ROUTES] [-s SERVICE_INSTANCES] [-a APP_INSTANCES] [--allow-paid-service-plans] [--reserved-route-ports RESERVED_ROUTE_PORTS]
                * $ cf update-quota QUOTA [-m TOTAL_MEMORY] [-i INSTANCE_MEMORY] [-n NEW_NAME] [-r ROUTES] [-s SERVICE_INSTANCES] [-a APP_INSTANCES] [--allow-paid-service-plans | --disallow-paid-service-plans] [--reserved-route-ports RESERVED_ROUTE_PORTS]
                * $ cf delete-quota QUOTA [-f]
                * **$ cf org ORG [--guid]：查看的信息中包含当前使用的org级quota plan**
            * space级的quota plan
                * $ cf **space-quotas**：列出所有space级的quota plan
                * $ cf space-quota SPACE_QUOTA_NAME：查询某个quota plan的详细信息
                * $ cf **create-space-quota** QUOTA [-i INSTANCE_MEMORY] [-m MEMORY] [-r ROUTES] [-s SERVICE_INSTANCES] [-a APP_INSTANCES] [--allow-paid-service-plans] [--reserved-route-ports RESERVED_ROUTE_PORTS]
                * $ cf update-space-quota SPACE_QUOTA [-i INSTANCE_MEMORY] [-m MEMORY] [-n NAME] [-r ROUTES] [-s SERVICE_INSTANCES] [-a APP_INSTANCES] [--allow-paid-service-plans | --disallow-paid-service-plans] [--reserved-route-ports RESERVED_ROUTE_PORTS]
                * $ cf delete-space-quota SPACE_QUOTA_NAME [-f]
        * App相关
            * 应用管理
                * $ cf **apps**：同时可以查看到URL等app的一些信息
                * $ cf **delete** APP_NAME
                * $ cf **start** APP_NAME
                * $ cf **stop** APP_NAME
                * $ cf **restart** APP_NAME
            * 推送应用
                * $ cf **push** APP_NAME [-b BUILDPACK_NAME] [-c COMMAND] [-f MANIFEST_PATH | --no-manifest] [--no-start] [-i NUM_INSTANCES] [-k DISK] [-m MEMORY] [-p PATH] [-s STACK] [-t HEALTH_TIMEOUT] [-u (process | port | http)] [--no-route | --random-route | --hostname HOST | --no-hostname] [-d DOMAIN] [--route-path ROUTE_PATH] [--var KEY=VALUE]... [--vars-file VARS_FILE_PATH]...
                * **推送前必须先指定org和space：$ cf target -s wyp-test**
                * 第一次push显示的是create信息，后面就是update了
                * 貌似yml文件中的host并不支持example中的变量${random-word}？
                * yml中可以不指定内存，默认是256M，硬盘是512M
            * 日志相关
                * $ cf **logs** APP_NAME
                    * cf logs APP_NAME --recent
            * 环境变量相关
                * [环境变量文档介绍](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html)
                * $ cf **env** APP_NAME
                * $ cf **set-env** APP_NAME ENV_VAR_NAME ENV_VAR_VALUE
                    * 比如cf set-env amp-ui-service **LOGGING_LEVEL_ROOT DEBUG**可以设置日志等级
                * $ cf **restage** APP_NAME
                    * 修改完日志等级后，会建议使用restage命令确保生效了
            * 其他
                * 查看app资源配置：$ cf **scale** APP_NAME [-i INSTANCES] [-k DISK] [-m MEMORY] [-f]
        * Service相关
            * $ cf **marketplace**
            * $ cf **services**：查看当前space有哪些service
            * $ cf create-services：Create a service instance
            * $ cf create-user-provided-service：Make a user-provided service instance available to CF apps
        * Domain相关
            * $ cf **domains**
        * Route相关
            * $ cf **routes**：列出当前space中所有注册的路由。即使app删掉了，其创建时自动添加的路由也还存在。
            * $ cf **map-route**
        * Buildpacks相关
            * $ cf **buildpacks**
        * $ cf **help** [-a]
            * 可以用于看一下都有哪些常用命令
        * $ cf curl PATH [-iv] [-X METHOD] [-H HEADER] [-d DATA] [--output FILE]
    * manifest.yml文件
        * **java的程序必须至少分配768M。**否则会启动失败，从日志中可以看到默认的256M是不行的：Memory available for allocation 256M is less than allocated memory

# 3. AMP
* MindSphere配置
    * Developer Cockpit
        * Application Details
            * Components（**作用是设置怎么把用户到MindSphere的请求转到Cloud Foundry。一个component是一个cloud foundry service，一个component可以配多个endpoint，endpoint是指用户URL中的哪些子路径可以映射到这个component-cloud foundry APP。**）
                * apigateway, https://ampapigateway-mmmdev.apps.cn1.mindsphere-in.cn, /uiservice/\*\*、/shiftcalendar/\*\*、/oee/\*\*、/oee-api/\*\*
                    * **说明不是转发所有的请求到cloud foundry。只转发这些。  **
                * ui, https://ampui-mmmdev.apps.cn1.mindsphere-in.cn, /\*、/assets/\*\*、/performance-monitoring/\*\*
                    * **/\*代表当前目录的所有，/\*\*代表当前目录及子目录的所有**
        * Roles & Scopes
            * core roles
                * mdsp:core:im.meIamViewer：user+admin
                * mdsp:core:assetmanagement.standarduser：user+admin
                * mdsp:core:iot.timUser：user+admin
                * mdsp:core:iot.filUser：user+admin
            * scopes
                * amp.oee.r：user+admin
                * amp.timezone.w：admin
                * amp.timezone.r：user+admin
                * amp.machinestatus.r：admin
                * amp.workpiece.w：admin
                * amp.workpiece.r：user+admin
                * amp.assignment.w：admin
                * amp.assignment.r：user+admin
                * amp.templates.w：admin
                * amp.templates.r：user+admin
                * amp.assets.r：user+admin
    *