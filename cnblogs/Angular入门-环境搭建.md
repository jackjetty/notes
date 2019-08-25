* 资源
    * [中文官网](https://www.angular.cn)
* 环境搭建
    * Node.js
        * [Node.js官网](https://nodejs.org/)
        * **一般本机安装最新的稳定版，服务端最好固定版本，慎重升级。**
        * 下x64的
        * Angular需要 Node.js 版本 10.9.0 或更高版本。
        * 要检查你的版本，请在终端/控制台窗口中运行 node -v
    * npm包管理器
        * 安装Node.js时默认已包含。
        * Angular、Angular CLI 和 Angular 应用都依赖于 npm 包中提供的特性和功能。
        * 要想下载并安装 npm 包，你必须拥有一个 npm 包管理器。
        * 如果网络不好，可以使用淘宝的**cnpm**
            * 全局安装：npm install -g cnpm -registry=https://registry.npm.taobao.org
    * Angular CLI
        * 你可以使用 Angular CLI 来创建项目、生成应用和库代码，以及执行各种持续开发任务，比如测试、打包和部署。
        * 要使用 npm 命令全局安装 Angular CLI，请打开终端/控制台窗口，输入如下命令：npm install -g @angular/cli
    * IDE
        * VS Code
            * 随便下个Windows x64装上行了。
            * 用VS Code进行Debug的话，需要调整一下默认生成的launch.json
                * 貌似调试前端的话，也只是相当于远程调试浏览器
        * StackBlitz在线开发环境
            * [第一个Angular项目](https://stackblitz.com/angular/qmrbdvqdnle?file=src%2Findex.html)
            * 可使用GitHub账号登录
            * 风格类似VS Code
            * 右侧是页面的模拟显示