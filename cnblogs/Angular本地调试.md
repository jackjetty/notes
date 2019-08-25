* 直接在Chrome中调试
    * 本地启动，不会走prod编译，所以代码一般不会压缩打包
    * 如果在Chrome中的Sources标签页中没有直接在localhost等除看到源码可以进行断点调试，那么就要去找一下**webpack://**站点下面的源文件
* 在VS Code中调试
    * 其实就是装了插件，可以远程调试浏览器，断点和浏览器上的断点同步执行
    * 不论什么语言，用VS Code进行调试，都是要在Debug窗口中新建一个Debug Setting，然后相应的修改生成的对应的launch.json文件中的启动等配置，如启动端口、启动的浏览器、启动的地址等。
    * 可以使用[**Augury**](https://augury.rangle.io/)
    * 可以在VS Code中搜索**Debugger for Chrome插件**，参考[在VSCode中调试angular的方法](https://blog.csdn.net/luanxiyuan/article/details/81171618)，安装完之后可以在Debug窗口中增加配置（自动 .vscode 文件夹中创建了 launch.json，要做相应的修改，如入口地址、端口等）：

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:4200",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```