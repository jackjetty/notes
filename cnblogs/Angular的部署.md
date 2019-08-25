* 编译结果
    * ![Angular file structure](https://img2018.cnblogs.com/blog/106125/201907/106125-20190730152126020-1617329703.png)
* 部署到线上环境必须的文件（也就是说大多数情况下，dist/下面的文件就够了）
    * dist/
    * package.json
    * app.js（如果自定义了接口或改了其他东西，需要从app.js启动时）
    * XXX（如果单独依赖了其他不在package.json下通过版本管理的常见包的话，需要把压缩包也引进来，但是package.json下也有相应的模块及检验码）