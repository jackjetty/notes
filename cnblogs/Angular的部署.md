* 编译结果
    * 必须的文件其实只有dist/、app.js、package.json和特别引入的文件/依赖包
    * 除了下图内容之外，其实还有一个**node_modules**文件夹，只不过在部署到CloudFoundry等环境时，不需要该文件夹，部署过程中会根据paskage.json的内容**重新下载依赖包**。
    * ![Angular file structure](https://img2018.cnblogs.com/blog/106125/201907/106125-20190730152126020-1617329703.png)
* 部署到线上环境必须的文件（也就是说大多数情况下，dist/下面的文件就够了）
    * dist/
        * **部署时必须带上**
    * package.json
        * **部署时必须带上**
        * 部署过程中会根据paskage.json的内容重新下载依赖包
    * app.js
        * **项目中不一定有该文件，但如果有，那么部署时也必须带上**
        * 如果自定义了接口或改了其他东西，需要从app.js启动时会有
    * XXX
        * 如果单独依赖了其他不在package.json下通过版本管理的常见包的话，需要把压缩包也引进来，但是package.json下也有相应的模块及检验码
        * **部署时必须带上**