* 各种结构风格？
    * 初始项目结构（加粗的是必须有的，普通的是一般会有的，斜体的是项目通常会自定义的）
        * **angular.json或者.angular-cli.json（工作区配置文件）**
            * 为工作区中的所有项目指定 CLI 的默认配置，包括 CLI 要用到的构建、启动开发服务器和测试工具的配置项，比如 TSLint，Karma 和 Protractor。
            * 在 angular.json 的顶级，一些属性用于配置工作区，其中的 projects 区则包含其余的针对每个项目的配置项。CLI 在工作空间级的默认设置可以被项目级的设置所覆盖，而项目级的设置可以被命令行中的设置所覆盖。
            * 还定义了项目的许多配置，如proxy
        * **package.json（工作区配置文件）**：配置工作空间中所有项目可用的 npm包依赖 。
            * 定义了name、version、license等基本信息
            * 定义了封装的命令
            * 定义了使用的第三方package及其版本
            **想要安装的依赖，有可能只是配了用哪个大版本，可以使用^、~等符号。^指使用该大版本下的最新版本。**
        * package-lock.json（工作区配置文件）：提供 npm 客户端安装到 node_modules 的所有软件包的版本信息。**真实安装的依赖，基本和package.json中的配置对应，但会有具体的真实下载的依赖包的版本、下载地址、完整性检查码、对其他包的依赖等。**
        * browserslist（应用配置文件）：可无。配置各种前端工具之间共享的目标浏览器和 Node.js 版本。编译时会依此调整输出的js和css。
        * karma.conf.js（应用配置文件）：应用专属的 Karma 配置。主要目标是为开发人员提供高效的测试环境。侧重于单元测试。是一个test runner，。
        * tsconfig.json（应用配置文件）：工作空间中各个项目的默认 TypeScript 配置。
        * tslint.json（应用配置文件）：工作空间中各个项目的默认 TSLint 配置。各种代码检查的配置。
        * README.md（工作区配置文件）：可无。根应用的简介文档。项目简介。
        * .editorconfig（工作区配置文件）：可无。代码编辑器的配置。如编码、缩进方式、换行、trim空格等。通用于各IDE，但可能需要安装插件，如VS Code需要安装EditorConfig for VS Code。
        * .gitignore（工作区配置文件）：可无。指定 Git 应忽略的不必追踪的文件。Git的忽略配置文件，即哪些文件/文件夹不需要由Git管理。
        * *app.js*：可无。自定义的入口文件，可使用node app.js命令启动应用。node.js语法，在webserver启动时执行，可以通过require引入node.js库并使用。
        * *protractor.conf.js*：可无。基于angular的前端自动化测试工具。侧重于端到端测试。
        * e2e/：端到端测试。常和protractor配合使用。
        * node_modules/
        * **src/**：根项目的源文件。
            * **index.html**在angular.json或者.angular-cli.json中配置。
            * **main.ts**：在angular.json或者.angular-cli.json中配置。入口？默认定义了environment.production为true时，启用enableProdMode();开关。
            * **polyfills.ts**
            * **styles.css**
            * **environments/**：在angular.json或者.angular-cli.json中配置。当使用ng build -prod命令时使用environment.prod.ts，否则使用environment.ts。会导致main.ts中是否会启用enableProdMode();开关。
                * **environment.prod.ts**
                * **environment.ts**
            * **assets/**
                * *css*
                * *images*
                * *fonts*
                * *icons*
                * *i18n*
                * *json*
            * **app/**
                * **app-routing.module.ts / app.routing.ts**
                * **app.module.ts**
                * **app.component.html**
                * **app.component.ts**
                * **app.component.css**