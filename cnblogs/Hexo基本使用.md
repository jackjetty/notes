#1. 简述
* Hexo是一个基于Node.js的博客系统，常用于搭建在GitHub上。
* 其本质其实是一个基于Node.js的内嵌webserver的程序，可以通过一些命令根据markdown生成静态页面，这些页面既可以部署到本地内嵌的webserver上，也可以部署到GitHub上（配置和命令）。

#2. 准备Git客户端和GitHub
* 安装Git客户端或TortoiseGit
* 注册GitHub
* 创建一个仓库，仓库命名必须为**用户名.github.io**，因为全部步骤完成后你就可以通过**https://用户名.github.io**来访问你的博客了~
* 配置SSH
        * 打开Git Bash，设置用户名（$ git config --global user.name "你的GitHub用户名"）和邮箱（$ git config --global user.email "你的GitHub注册邮箱"）
        * 生成ssh密钥：$ ssh-keygen -t rsa -C "你的GitHub注册邮箱"，此时在用户文件夹下就会有一个新的文件夹.ssh，其中id_rsa文件是私钥，要妥善保管，id_rsa.pub是公钥文件
        * 添加ssh密钥到GitHub：在Settings->SSH and GPG keys下，新建一个ssh key，将id_rsa.pub中的内容复制到Key文本框中
        * 测试ssh：$ ssh -T git@github.com

#3. 安装配置Hexo
* 从官网安装Node.js
* 安装Hexo：npm install hexo-cli -g
* 初始化Hexo：hexo init
* 安装依赖：npm install
* 启动Hexo：hexo server，启动后可以在浏览器中使用http://localhost:4000访问本地webserver（已自动根据现有文章生成静态页面）

#4. 集成Hexo与GitHub
* 认证已通过前面的ssh实现
* 编辑Hexo配置文件_config.yml，添加github仓库信息（注意type、repo、branch的前面有两个空格，后面的:后面有一个空格）：
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:wangyeping1988/wangyeping1988.github.io.git #github仓库地址
  branch: master # github分支
```
* 安装Git插件：npm install hexo-deployer-git --save

#5. 日常使用
* 新建文章（可以用双引号括起来）：hexo new {title}
    * 然后在source文件夹下就生成了对应的md文件，就可以直接去编辑了
* 生成静态页面：hexo generate
* 清除生成内容：hexo clean
* 部署到GitHub：hexo deploy

#6. 使用模板