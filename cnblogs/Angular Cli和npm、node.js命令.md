# 1. Node.js
    * **node -v**
        * node.js本身的版本
    * **npm**
        * 是其命令行工具，但其实是**包管理工具**
        * 常用命令
            * **npm -v**
                * npm包管理器本身的版本
            * **npm help [command name]**
            * **npm [command name] -h**
            * **npm install**
            * **npm build**
            * **npm start**
            * **config**
                * npm config list [--json]
                * npm config set xxx xxx
                * npm config get xxx
                * npm config delete xxx
                * npm set <key> <value>
                * npm get [<key>]

# 2. AngularJS Cli（脚手架）
    * **ng**
        * AngularJS自己的命令行工具
        * 使用npm install -g @angular/cli命令安装
        * 可以在package.json文件的scripts部分自定义命令
        * 常用命令
            * **ng version或ng  v**
                * 查看当前项目使用的所有package及其版本
            * **ng [command name] --help**
            * **ng build [--prod]**：prod时会启用environment.prod.ts，然后也就间接在main.ts中启用了enableProdMode()，会在编译时做压缩等操作
            * **ng serve**
            * **ng config**
            * ng e2e
            * ng test