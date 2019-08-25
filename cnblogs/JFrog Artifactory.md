* 是一款二进制存储管理工具，用来管理构建构建工具（如：gradle、maven、nuget、docker等等）等所依赖的二进制仓库，以方便管理第三方库和发布目标版本库，从而提高软件开发效率。
* 为DevOps服务
* 可以链接、缓存其他artifactory？
* 认证
    * 可以和域账号关联
    * 登陆后，在右上角点击用户名打开profile页面，可以点击unlock按钮
        * **这个所谓的lock，并不是说账号锁住了，还是可以登录网站的，而是指token时效了，需要重新unlock后使用**？？
        * 会自动生成一个用于gradle等访问repo的token，生成的token可以使用在gradle的配置文件中连接maven时使用
            * 每次域账号修改密码后可以并且也需要手动重新unlock生成token
            * 如果可以像后面rest api的refresh token接口那样，用一个固定的refresh token定时刷新生成一个token，不就可以不用手动在页面unlock了？可以集成到构建脚本中了就
        * 也可以手动点一下再生成一个access api token用于调用rest api
* 使用
    * 包含很多repo，有的是公共的，有的需要给用户授权
    * 可以在"Artifactory Repository Browser"页面以展开、搜索的形式查看有哪些repo，每个repo下面又有什么东西，当然可能会很多很慢
    * 可以在搜索页面中搜索jar包等资源。可以指定在哪个repo中搜索。**貌似搜索词不能太短，或者要输的比较完整？区分大小写？比如mta搜不出来，mtacommon就可以**
* Rest API
    * 提供了一些实用的REST API
    * 认证也是基于OAuth认证的
    * 要先获取api token，在访问接口带上（也是Bearer认证？）
    * 可以使用curl、postman等工具去访问
    * 比如有create token、refresh token的接口，前者是用账号密码创建一个token（貌似必须是管理员），后者是根据refresh token刷新和重新生成一个新的有效的token（**这个token不是access token，是用来gradle等访问repo用的token？**）？