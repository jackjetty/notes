* [Getting started with GitLab CI/CD](https://code.siemens.com/help/ci/quick_start/README)
* [GitLab CI/CD Examples](https://code.siemens.com/help/ci/examples/README.md)
* .gitLab-ci.yml
    * [GitLab CI/CD Pipeline Configuration Reference](https://code.siemens.com/help/ci/yaml/README.md)
    * pipeline及其中的job的定义
* Runner
    * 运行在编译机上的client，用于与GitLab通讯，并执行编译等任务。
    * 貌似是主动去GitLab上拿job来执行，而不是GitLab来触发Runner。
    * [GitLab Runner Docs](https://docs.gitlab.com/runner/)
    * 安装/注册过程（Windows）
        * [Install GitLab Runner on Windows](https://docs.gitlab.com/runner/install/windows.html)
        * 创建安装目录
        * 下载安装包至安装目录，并重命名为gitlab-runner.exe
        * 打开PowerShell ISE（貌似其实PowerShell、CMD也是可以的）
        * 注册Runner
            * [Registering Runners](https://docs.gitlab.com/runner/register/index.html#windows)
            * 切换到程序目录
            * 执行./gitlab-runner.exe register（CMD中不加./）
            * 输入gitlab-ci coordinator URL，即GitLab instance URL，去https://code.siemens.com/mindsphere_snc/mcapps_ali/-/settings/ci_cd项目的CI/CD配置中获取，如https://code.siemens.com/
            * 输入Specific Runner的GitLab-ci token，去https://code.siemens.com/mindsphere_snc/mcapps_ali/-/settings/ci_cd项目的CI/CD配置中获取，如KqMg5Mayot5anG4Wz6G_
            * 输入描述，后面在GitLab的界面上还能再改
            * 输入这个Runner的tags，后面在GitLab的界面上还能再改
                * [Using tags](https://docs.gitlab.com/ee/ci/runners/#using-tags)
            * **此时如果网络不通，就会出错。如果用的是公司网络，可以在CMD等命令环境中设置http_proxy和https_proxy环境变量为cntlm地址，cntlm配置成走公司代理。**
            * 输入executor类型
                * [https://docs.gitlab.com/runner/executors/README.html](https://docs.gitlab.com/runner/executors/README.html)
                * ssh不推荐，没用docker什么的话，可以简单先用shell
        * 安装Runner为系统服务（貌似是CMD的写法，powershell会不同）
            * 推荐使用Built-in System Account
                * **以管理员权限执行命令**
                * gitlab-runner install
                * gitlab-runner start
            * 也可以使用user account
                * gitlab-runner install --user ENTER-YOUR-USERNAME --password ENTER-YOUR-PASSWORD
                * gitlab-runner start
        * 安装注册完成后，程序目录会有一个config.toml配置文件
        * https://code.siemens.com/mindsphere_snc/mcapps_ali/-/settings/ci_cd项目的CI/CD配置中还可以查看当前有哪些Runner，以及他们的状态，还能暂停或者删除它们。

```
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "wyp_pc_cf_checker_winform"
  url = "https://code.siemens.com/"
  token = "ZuzrxzyVikCT4XWgsLbW"
  executor = "shell"
  shell = "powershell"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
  [runners.custom]
    run_exec = ""

```

    * 配置过程
        * [Configuring GitLab Runners](https://code.siemens.com/help/ci/runners/README.md)