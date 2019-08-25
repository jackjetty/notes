* 通过Cloud Foundry的Gateway访问
    * 两种方式
        * APP注册后，默认就可以通过Cloud Foundry的Gateway进行访问，如https://gateway.cn1.xxx.cn/api/appname-tenantname/v1/v3/controllername/actionname/param1
        * APP可以通过postman和平台管理账号调用平台的相关接口，修改类型为API，那么该APP的接口访问地址就变成了https://gateway.cn1.xxx.cn/api/apiname-tenantname/v3/ontrollername/actionname/param1
    * 两种方式都需要进行账号登录
        * 通常可以使用technical user账号
        * 如果通过postman和平台管理账号调用平台的相关接口，修改接口为unsecured，那么在访问时就不再需要使用账号登录
* 通过Mindsphere等Cloud Foundry的PaaS外层包装来访问
    * 需要先在Mindsphere上注册Component信息，包括该app对应的Cloud Foundry内部route地址，以及路径过滤和映射