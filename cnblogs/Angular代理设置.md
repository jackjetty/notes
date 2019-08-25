* 代理
    * 转发前端请求到真实服务端（可以附加cookie信息）或者Mock Server
    * 方式一
        * 封装的命令中可能会用到代理，即给npm start实际上使用的ng serve命令传参proxy-config
            * package.json中封装的脚本部分如下：

```
  "scripts": {
    "ng": "ng",
    "start": "ng serve --proxy-config proxy.conf.json",
    "build": "ng build --prod",
    "test": "ng test --sourcemaps=false",
    "lint": "ng lint",
    "e2e": "ng e2e",
    "ci": "ng build --prod && ng test --browsers ChromeHeadlessCI --single-run --code-coverage"
  }
```

            * proxy.conf.json配置文件如下，会根据定义重定向请求：

```
{
  "/uiservice/*": {
    "target": "http://localhost:4300",
    "secure": false
  },
  "/api/im/v3/Users/me": {
    "target": "http://localhost:4300",
    "secure": false
  },
  "/shiftcalendar/*": {
    "target": "http://localhost:4300",
    "secure": false
  },
  "/oee/*": {
    "target": "http://localhost:4300",
    "secure": false
  }
}
```

    * 方式二
        * 定义一个.local-user-config.json文件存储此时真实网站的真实cookie信息，用于转发请求时附上

```
{
	"sessionId":"OTRlMWZjODYtZTJhYy00MTFiLTg1YjctMjc1MjQ2OTFiMTM3",
	"xsrfToken":"6a81d1d4-0a48-4175-83c4-c177c941d527"
}
```

        * 定义proxy.config.js用来设置代理配置，如路由、转发目标url。

```
const { proxySettingsFactory } = require("./proxy-settings-factory");

const targetUrl = 'https://mcapps-mmmui-mcapps.eu1.mindsphere.io/';

const PROXY_CONFIG = [
  proxySettingsFactory(["/api/**", "!/api/webcomponents/**"], "^/api/.*$", targetUrl),
  proxySettingsFactory(["/v3/**"], "^/v3/.*$", targetUrl)
]
module.exports = PROXY_CONFIG;
```

        * 定义proxy-settings-factory，包含具体的转发逻辑代码，如附上cookie信息

```
'use strict';

const fileSystem = require('fs');
const HttpsProxyAgent = require('https-proxy-agent');

const fileName = '.local-user-config.json';
const userConfig = JSON.parse(fileSystem.readFileSync(fileName));

const corporationProxy = process.env.HTTPS_PROXY || process.env.https_proxy || process.env.HTTP_PROXY || process.env.http_proxy;
const agent = new HttpsProxyAgent(corporationProxy);


/**
 *
 * @param {Array|function} context Function or Array to filter the required context
 * @param {String} regExPattern Regular Expression String to filter the required pattern
 * @param {String} url The Target URL behind the proxy
 */
function proxySettingsFactory(context, regExPattern, url, noProxy = false) {
    const config = {
        context: context,
        target: url,
        secure: false,
        bypass: req => {
            if (new RegExp(regExPattern, 'i').test(req.url)) {
                req.headers.Cookie = `SESSION=${userConfig.sessionId}; XSRF-TOKEN=${userConfig.xsrfToken}; `;
                req.headers['X-XSRF-TOKEN'] = userConfig.xsrfToken;
                req.headers.origin = url;
            }
        },
        changeOrigin: true,
        logLevel: 'info'
    };

    if(!noProxy) {
        config.agent = agent;
    }

    return config;
}
exports.proxySettingsFactory = proxySettingsFactory;
```

        * 最后要在angular.json中定义serve命令时使用该proxy配置（只有l本地开发时才走这个proxy配置？）

```
				"serve": {
					"builder": "@angular-devkit/build-angular:dev-server",
					"options": {
						"browserTarget": "mmmui:build",
						"proxyConfig": "webpack/proxy.config.js",
						"open": true,
						"port": 4205
					},
					"configurations": {
						"production": {
							"browserTarget": "mmmui:build:production"
						},
						"local": {
							"browserTarget": "mmmui:build",
							"proxyConfig": "webpack/proxy.local.config.js",
							"open": true,
							"port": 4205
						}
					}
				},
```