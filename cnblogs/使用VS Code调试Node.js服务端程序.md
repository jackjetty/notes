* [vscode 调试node.js ](https://www.cnblogs.com/klsw/p/7765427.html)
* Node.js服务端程序好像也可以在浏览器上调试，但是没必要吧，毕竟是后端代码。
* 步骤
    * 进入VS Code左侧树上的Debug标签页
    * 在左上角点击下拉菜单中的“Add Configuration...”新建一个Debug配置（选择程序类型，此处为**Node.js: Launch Program**），即**launch.json**调试启动配置文件，并自动进入
    * 自行修改launch.json中的**name**值，即当前Debug配置的名称（一个VS Code可以有多个，对应不同的项目或语言环境）
    * 自行修改launch.json中的**program**值，即指定启动文件，对于Node.js服务端程序，一般是app.js的路径，如${workspaceFolder}/tools/MockServer/app.js。
    * 然后就可以在左上角选择想要用的Debug Configuration，然后点击绿色的开始箭头按钮就开始Debug调试了（自己在文件中设断点）。