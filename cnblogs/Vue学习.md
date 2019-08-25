#1. 小技巧
* 经常编译会报一堆错误，都是格式方面的，比如函数定义中()前面要有空格、缩进应该是4个而不是6个等，这是因为设置了eslint，如果你不想有规范的js代码，可以重新初始化关掉eslint。
    * 在bulid/webpack.base.conf.js里面有配置：module: { rules: [                 ...(config.dev.useEslint ? [createLintingRule()] : []),
    * 点进config.dev.useEslint，发现在config/index.js里配置useEslint: true, // 改为false即可
* 发起ajax请求通常用axios，需要通过node.js安装依赖
    * vue本身不支持发送AJAX请求，需要使用vue-resource（vue1.0版本）、axios（vue2.0版本）等插件实现
    * axios是一个基于Promise的HTTP请求客户端，用来发送请求，也是vue2.0官方推荐的，同时不再对vue-resource进行更新和维护
    * 安装依赖：npm install axios
    * axios.post(url,data,[options]);
    * axios默认发送post数据时，数据格式是Request Payload，并非我们常用的Form Data格式，所以参数必须要以键值对形式传递，不能以json形式传参
* 有时会用css的扩展sass，需要通过node.js安装依赖
    * npm install stylus-loader css-loader style-loader --save-dev
    * npm install sass-loader --save;
    * npm install node-sass --save;
* 为什么有时候编译后，要把链接结尾的/#/删掉，重新访问才行？？

#2. VSCode插件
* 使用 vscode 开发 vue, 必不可少的插件包括 vetur（辅助编程）, prettier（格式化）
    * 文件->首选项->设置中搜索vetur，修改配置（可能只能单独修改用户配置，其他如css保留为prettier）：
```
{
    "vetur.format.defaultFormatter.html": "js-beautify-html",
    "vetur.format.defaultFormatter.js": "vscode-typescript",
    "prettier.tabWidth": 4
}
```