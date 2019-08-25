* 使用环境变量
    * node.js中可以读取环境变量，如上例中的process.env.mdsp_osbar_url
    * 除了可以读取运行环境（如Cloud Foundry或操作系统）的环境变量，还可以读取node.js自身的环境变量
    * node.js自身的环境变量分为全局级、用户级、terminal级
        * [官方文档](http://nodejs.cn/api/process.html#process_process_env)
        * [Node.js中环境变量process.env的一些事详解](https://www.jb51.net/article/126838.htm?&_=1523688296390)
        * 可以使用命令npm config list、npm config set来查看、设置node.js的环境变量
    * 使用方式：

```
app.get('/mdsp-osbar', (req, res) => {
  console.log('mdsp_osbar_url:', process.env.mdsp_osbar_url);
  if (process.env.mdsp_osbar_url) {
    res.redirect(process.env.mdsp_osbar_url);
  } else {
    res.redirect("https://static.eu1-b.mindsphere.io/osbar/v4/js/main.min.js");
  }
});
```

* 使用environment部署变量
    * 每个Angular工程都有一个environments目录，其下有**environment.ts和environment.prod.ts**两个文件，分别存放了部署到不同环境时不同的配置，默认只有production: true
    * 最终是**ng build --prod**的这个参数导致使用了environment.prod.ts
    * src根目录下的**.angular-cli.json**文件是Angular所有CLI命令的配置
        * .angular-cli.json中在apps节点下设置了入口main程序文件名称
        * .angular-cli.json中在apps节点下设置了入口environment相关的配置

```
  "apps": [
    {
      "root": "src",
      "outDir": "dist",
      "assets": [
        "assets",
        "favicon.ico",
        {
          "glob": "**/*",
          "input": "./node_modules/sinint-application-core-spa/assets/icons/",
          "output": "./src/assets/icons/"
        }
      ],
      "index": "index.html",
      "main": "main.ts",
      "polyfills": "polyfills.ts",
      "test": "test.ts",
      "tsconfig": "tsconfig.app.json",
      "testTsconfig": "tsconfig.spec.json",
      "prefix": "app",
      "styles": [
        "./assets/css/default.theme.css",
        "styles.scss"
      ],
      "scripts": [
        "../node_modules/jquery/dist/jquery.min.js",
        "../node_modules/jqueryui/jquery-ui.js"
      ],
      "environmentSource": "environments/environment.ts",
      "environments": {
        "dev": "environments/environment.ts",
        "prod": "environments/environment.prod.ts"
      }
    }
  ]
```

    * src根目录下的这个**main.ts**文件，其中会在production值为true时才**enableProdMode()**，这个值会让编译结果不同，压缩，提高性能，关掉一些功能？

```
import { enableProdMode } from '@angular/core';

import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}
```