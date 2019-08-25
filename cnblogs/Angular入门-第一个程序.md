* 资源
    * [中文官网](https://www.angular.cn)
* 第一个程序
    * 创建工作空间和初始应用
        * 在工作目录使用**ng new [projectname]**来创建一个新的项目，并且会自动创建项目代码结构骨架、e2e测试项目结构骨架。
            * ng new 命令会提示你提供要把哪些特性包含在初始应用中。按 Enter 或 Return 键可以接受默认值。
                * 会让你选择是否使用Angular路由模块。Would you like to add Angular routing?
                * 会让你选择要使用的样式表格式，通常包含CSS、SCSS、Sass、Less、Stylus。Which stylesheet format would you like to use? (Use arrow keys)
            * Angular CLI 会安装必要的 Angular npm 包和其他依赖包。这可能要花几分钟的时间。
    * 运行/调试应用
        * Angular CLI 中包含一个服务器，方便你在本地构建和提供应用。
        * 步骤
            * 转到 workspace 文件夹（my-app）。
            * 使用 CLI 命令 ng serve 和 --open 选项来启动服务器。
                * ng serve 命令会启动开发服务器、监视文件，并在这些文件发生更改时重建应用（自动重建）。
                * --open（或者只用 -o 缩写）选项会自动打开你的浏览器，并访问 http://localhost:4200/。