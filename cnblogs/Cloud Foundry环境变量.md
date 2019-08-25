* 参考
    * [Cloud Foundry Environment Variables](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html)
    * [User-Provided Service Instances - Deliver Service Credentials to an App](https://docs.cloudfoundry.org/devguide/services/user-provided.html#credentials)
    * [Delivering Service Credentials to an App - Using Bound Service Instances](https://docs.cloudfoundry.org/devguide/services/application-binding.html#use)
* 相关命令
    * 查看所有的环境变量：cf env appname
    * 新增用户自定义环境变量：cf set-env APP_NAME ENV_VAR_NAME ENV_VAR_VALUE
    * 删除用户自定义环境变量：cf unset-env APP_NAME ENV_VAR_NAME
* System-Provided，系统自动生成的
    * VCAP_APPLICATION
        * 自动生成的APP的信息
    * VCAP_SERVICES
        * 使用cf bind-service命令绑定service或者user provided service后，自动生成的service的信息，包含credential信息。
            * **绑定service只是为了自动生成连接信息、credential信息并按指定格式放到System-Provided环境变量中，并可以用于配置的自动注入，其实本质上最终还是用的这些host、port、username、password等信息去连接的service，并不对网络通不通（如跨space访问service）有影响？**
        * service的信息（包含credential信息），可以通过两种方式在代码中使用（用于操作service）
            * 自行解析和使用JSON格式的环境变量中VCAP_SERVICES下面的对应用于连接的service信息（Parse the JSON yourself）
            * **自动注入service配置（Auto-configuration）**
                * 不同语言、框架的自动注入方式不太一样，但对使用者都是透明的
                * [第一步：所有语言的buildpack列表](https://docs.cloudfoundry.org/buildpacks/)
                * [第二步：该语言的buildpack信息页面](https://docs.cloudfoundry.org/buildpacks/java/index.html)
                * [第三步：找到对应的不同框架的Configuring Service Connections的方式列表](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/index.html)
                * [第四步：如spring框架下的MySQL和PostgresSql的连接配置自动注入](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#rdbms)
                    * Cloud Foundry会自动帮你去找javax.sql.DataSource bean，并为driverClassName、url、username、password、validationQuery等自动赋值。
* User-Provided，用户自定义的
    * spring框架下
        * 对应application.properties文件中的配置项
        * 环境变量优先级更高，会覆盖application.properties文件中的值
        * 与application.properties文件中的key格式不太一样，application.properties文件中是x.x.x，环境变量中是x_x_x，并且不区分大小

```
System-Provided:
{
 "VCAP_SERVICES": {
  "rabbitmq36": [
   {
    "binding_name": null,
    "credentials": {
     "host": "rad65420b.service.dc1.a9ssvc",
     "hosts": [
      "rad65420b-mq-0.node.dc1.a9ssvc"
     ],
     "http_api_uri": "http://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b.service.dc1.a9ssvc:15672/api",
     "http_api_uris": [
      "http://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b-mq-0.node.dc1.a9ssvc:15672/api"
     ],
     "password": "a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2",
     "port": 5672,
     "protocols": {
      "amqp": {
       "host": "rad65420b.service.dc1.a9ssvc",
       "hosts": [
        "rad65420b-mq-0.node.dc1.a9ssvc"
       ],
       "password": "a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2",
       "port": 5672,
       "ssl": false,
       "uri": "amqp://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b.service.dc1.a9ssvc:5672",
       "username": "a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf"
      },
      "management": {
       "host": "rad65420b.service.dc1.a9ssvc",
       "hosts": [
        "rad65420b-mq-0.node.dc1.a9ssvc"
       ],
       "password": "a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2",
       "path": "/api",
       "port": 15672,
       "ssl": false,
       "uri": "http://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b.service.dc1.a9ssvc:15672/api",
       "uris": [
        "http://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b-mq-0.node.dc1.a9ssvc:15672/api"
       ],
       "username": "a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf"
      }
     },
     "ssl": false,
     "uri": "amqp://a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf:a9s8365be64aeeb8b6edad572027cd2c704b6ddbaf2@rad65420b.service.dc1.a9ssvc:5672",
     "username": "a9s-brk-usr-286fff686f038dcb4f9f291fa18d474346dc30bf"
    },
    "instance_name": "mmm-rabbit",
    "label": "rabbitmq36",
    "name": "mmm-rabbit",
    "plan": "rabbitmq-xs",
    "provider": null,
    "syslog_drain_url": null,
    "tags": [
     "messaging",
     "queue"
    ],
    "volume_mounts": []
   }
  ],
  "user-provided": [
   {
    "binding_name": null,
    "credentials": {
     "host": "pod63d915-psql-master-alias.node.dc1.a9ssvc",
     "hosts": [
      "pod63d915-pg-0.node.dc1.a9ssvc"
     ],
     "name": "pod63d915",
     "password": "a9saa4ad99b7c666e8f3b2f549ca6f67055fb5be38e",
     "port": 5432,
     "uri": "postgres://a9se76b8977394a1ec5a78bcec5e8331fa4b9b989ce:a9saa4ad99b7c666e8f3b2f549ca6f67055fb5be38e@pod63d915-psql-master-alias.node.dc1.a9ssvc:5432/pod63d915",
     "username": "a9se76b8977394a1ec5a78bcec5e8331fa4b9b989ce"
    },
    "instance_name": "mmm-postgres",
    "label": "user-provided",
    "name": "mmm-postgres",
    "syslog_drain_url": "",
    "tags": [],
    "volume_mounts": []
   }
  ]
 }
}

{
 "VCAP_APPLICATION": {
  "application_id": "6f6e9fb0-e79d-4a5a-9fb8-10276cb1097d",
  "application_name": "OeeMachineStatusGenerator",
  "application_uris": [
   "oeemachinestatusgenerator-demoopt.apps.cn1.mindsphere-in.cn"
  ],
  "application_version": "20b20ca3-da1d-49f3-a52e-b9f66c4297a3",
  "cf_api": "https://api.cf.cn1.mindsphere-in.cn",
  "limits": {
   "disk": 512,
   "fds": 16384,
   "mem": 750
  },
  "name": "OeeMachineStatusGenerator",
  "space_id": "90c271c7-58ea-4831-ae08-3ffa6f221a03",
  "space_name": "mmmbackend-1.6.2",
  "uris": [
   "oeemachinestatusgenerator-demoopt.apps.cn1.mindsphere-in.cn"
  ],
  "users": null,
  "version": "20b20ca3-da1d-49f3-a52e-b9f66c4297a3"
 }
}

User-Provided:
Deployed_Changeset: 12015
SVC_URL_BASICSERVICE: https://sinumerikbasicservice-demoopt.apps.cn1.mindsphere-in.cn/
TECHUSER_INFO: [{"techuser_id":"prmopt3rdpartytu", "techuser_secret":"xsfe2e8a-0f19-kc04-8m6t-hj3p7rr107v1", "techuser_authtype":"basic", "token_uri":"https://demoopt.piam.cn1.mindsphere-in.cn/uaa/oauth/token?grant_type=client_credentials", "tenant":"demoopt"}]
deployed_version: OeeMachineStatusGenerator-01.06.00.00.12015-RELEASE.jar
gitBranch: RELEASE/01.06.00.00
management.port: -1
mdsp_os_vm_app_name: mmmbackend
mdsp_os_vm_app_version: 1.6.2
oauth2_uaaPublicKeyUrl: https://demoopt.piam.cn1.mindsphere-in.cn/uaa/token_keys
server_tomcat_max_http_header_size: 16384
spring_profiles_active: secured
techuser_t1_tenant: prmopt3rdpartytu

No running env variables have been set

No staging env variables have been set
```