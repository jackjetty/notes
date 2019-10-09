* 和JavaScript语法很类似
    * Json序列化
        * JSON.stringify(object)
        * JSON.parse("string")
    * 控制台输出日志： console.log("");
    * 正则表达式：new RegExp("some regEx Pattern", 'i').test(req.url)
* 修改http请求头
    * req.headers.cookie = `SESSION=${userConfig.sessionId}; XSRF-TOKEN=${userConfig.xsrfToken}; `;
        * 主要貌似旧版本的Node.js中可以使用大写的Cookie成员变量，而新的只能用小写的cookie
    * req.headers['X-XSRF-TOKEN'] = userConfig.xsrfToken;
    * req.headers.origin = url;
* 文件系统操作
    * const fileSystem = require('fs');
    * fileSystem.readFileSync(fileName)