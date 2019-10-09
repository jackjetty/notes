* package.json
    * npm install会依此下载依赖包，放到项目的node_modules目录下
    * "dependencies"中定义了需要依赖包（module）和版本
    * "devDependencies"中定义了开发环境需要以来包（module）和版本
    * '^'（插入符号）: 这个符号就显得非常的灵活了，他将会把当前库的版本更新到当前major version（也就是第一位数字）中最新的版本。放到我们的例子中就是："vue": "^2.2.2", 这个库会去匹配2.x.x中最新的版本，但是他不会自动更新到3.0.0。
    * '~'（波浪符号）:他会更新到当前minor version（也就是中间的那位数字）中最新的版本。放到我们的例子中就是："exif-js": "~2.3.0"，这个库会去匹配更新到2.3.x的最新版本，如果出了一个新的版本为2.4.0，则不会自动升级。波浪符号是曾经npm安装时候的默认符号，现在已经变为了插入符号。
* package-lock.json
    * npm install实际下载的依赖包（module）和版本
    * 可能与package.json中定义的版本不同，因为package.json中可能会用^、~等表达式模糊指定版本
* npm install
    * 根据package.json定义的依赖包（module）和版本去下载和放到项目中
* 查看npm的相关配置及环境变量配置
    * npm config list
* 缓存
    * 全局缓存位置：C:\Users\{username}\AppData\Roaming\npm-cache
    * 貌似也有一部分依赖于TypeScript的依赖包的位置在：C:\Users\z003xxxe\AppData\Local\Microsoft\TypeScript\{version}\node_modules
    * 修改缓存位置
        * npm config set prefix "D:\ProgramFile\nodejs\node_modules\node_global"
        * npm config set cache "D:\ProgramFile\nodejs\node_modules\node_cache"
* 设置npm镜像源
    * npm config set registry https://registry.npm.taobao.org --global
    * npm config set disturl https://npm.taobao.org/dist --global