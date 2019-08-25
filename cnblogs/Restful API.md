* **基础是URI**，所有的操作都是**基于资源**的，不同http action对应资源的不同操作
* url上不能有动词（如login），都要是名词加参数
* GET列表这种情况下，单词要用复数
* ID放在url中，不用放到参数中
* 所有操作都要设计成是**无状态**的
* 通常可以使用Linux上面的curl、Windows上的curl（要另外安装）和Postman来进行接口的测试。或者使用接口服务提供的Swagger-ui。
    * **curl可以使用-H -X --data等指定头部、method、数据**
    * curl可以使用-F参数指定文件上传
    * 再加一个-v参数可以看到一些详细信息
* **比较特别的是登陆/退出**，因为貌似这两个接口动作没有资源。这时候要转变一下思路，这里的资源其实是授权（authorization），POST是登陆，DELETE是退出。
    * 设计接口时，如果觉得对当前资源进行增删改查都不合适，那么就要重新考虑操作的资源到底是谁
* API版本
    * 只能把请求的接口的版本放到URL或者Request Header中
    * URL（直观，容易理解，但有点违反rest的原则）
        * 可以通过部署来解决，比如通过Nginx使用不同的域名或不同的前缀（比如.../v1/...），导向不同的服务或者说接口
        * 修改application.yml中的contextPath：server.servlet.contextPath: /tutorial-v2，即部署不同的spring-boot微服务，只是配置不同，使用不同的端口，然后Nginx中配置导向不同的spring-boot微服务
        * 修改@RequestMapping中的参数，比如@RequestMapping("/v1/tvseries")
        * 增加request的参数，比如/tvseries?version=2，然后服务端简单的做相应修改，可以在spring的controller中修改注解，比如@GetMapping(value="/{id}", params="version=2")
    * Request Header（缺点是一些缓存服务器只管url，所以可能导致乱掉）
        * 自定义request header，比如Version: 2，可以在服务端使用@GetMapping(value="/{id}", headers="Version=1")注解来对应上接口
        * 使用Accept（即使用标准的http header），比如Accept: application/vnd.xxx.v2+json，也算是一个标准的http头，可以在服务端使用@GetMapping(value="/{id}", produces="application/vnd.xxx.v2+json")注解来对应上接口
    * 自定义ApiVersion注解（RequestMappingHandlerMapping）
        * 使用@ApiVersion注解，但是需要自定义一个继承自RequestMappingHandlerMapping的类，然后自己实现把request分配到某个方法的规则（比如根据方法名、注解等信息），实现比较麻烦，适用于版本复杂的情况，需要实现
* 目前有流行的成熟的接口定义规范，如Open API 3.0