#1. 参考/转载
[vs code进行c/c++开发](https://www.cnblogs.com/tsfh/p/8900700.html)
[VSCode 的第一个C++程序（windows）[更新2018.10.28]](https://blog.csdn.net/qq_34347375/article/details/80641000)

#2. C++开发相关插件（扩展商店中直接搜索）
* 至少要装C/C++。
* 还可以安装Code Runner、C++ Intellisense和Include Autocomplete。

#3. 编译器
由于VS Code及其各个C++插件都没有C++编译器，所以需要自己装一个然后在项目中配置使用。
* 常用的有：
    * Windows下的mingw-w64
        * [装sourceforge上的64位的离线安装包！](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)，这个页面链接在官网的下载页面也能找到，其中x86_64是64位系统用的版本，现在我们一般用的是64位系统，所以我们可以在四个x86_64中选一个下载。seh结尾是纯64位编译。sjlj结尾是32 64两种编译，需加-m32或-m64参数。posix通常用于跨平台，比win32兼容性好一些。
        * [官网download表格中的链接](http://www.mingw-w64.org/doku.php)的在线安装包在国内一般用不了。
        * 最好将解压后的bin目录路径加到环境变量的末尾，如";C:\MinGW64\bin"，当然在VS Code的C++工程中后面生成的launch.json中也可以指定miDebuggerPath为该目录。

#4. 开发过程
##4.1. 新建工程文件夹并用VS Code打开
##4.2. 创建cpp源文件并编码
##4.3. 编译
使用ctrl + shift +b编译当前cpp源文件，第一次编译时会提示你配置生成任务"No build task to run found. Configure Build Task..."，选择"Create task.json file from template"，然后选择"Others"（其他还有MSBuild、Maven、.NET Core），就会生成一个tasks.json文件：
```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "echo",
            "type": "shell",
            "command": "echo Hello"
        }
    ]
}
```
需要把默认的示例task配置改为编译task配置，因此修改为：
```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "g++",
            "args": [
                "-g",    //表示在生成的可执行文件中加入调试信息,如果你要调试程序,-g参数必不可少
                "-oMySecondCPlus.exe",    //编译完成可以看到生成了a.exe文件，这是编译生成的默认文件名称，想要自定义文件名可以使用 -o参数，后面不要有空格
                "main.cpp"
            ],
            "presentation": {
                "reveal": "never"
            },
            "problemMatcher": [
                "$gcc"
            ]
        }
    ]
}
```
再次使用快捷键ctrl + shift +b并选择名为"build"的task就可以编译main.cpp了。
##4.4. 调试
选择VS Code左侧的调试界面，最上面的绿色启动按钮旁显示"No Configurations"，因此需要点击后添加配置，选择C++(GDB/LLDB)，即会生成launch.json文件：
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "enter program name, for example ${workspaceFolder}/a.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "/path/to/gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
这里需要修改的配置有：修改gdb编译器路径、修改生成的exe程序路径及名称（默认的a.exe或与task.json中配置的自定义名称相同）、配置每次调试前先进行编译（免得每次都要先手动build生成exe）。
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "D:\\documents\\own\\projects\\MySecondCPlus\\MySecondCPlus.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "C:\\MinGW64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "build"
        }
    ]
}
```