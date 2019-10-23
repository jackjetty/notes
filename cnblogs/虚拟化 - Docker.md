* Docker Desktop (for windows and mac)
    * [Docker Desktop](https://www.docker.com/products/docker-desktop)
    * 注意
        * 安装时有可能卡主，可以关掉重新装
        * 重启启动时，可能报错“VIRTUALIZATION MUST BE ENABLED”
            * https://docs.docker.com/docker-for-windows/troubleshoot/#virtualization-must-be-enabled
            * https://github.com/docker/for-win/issues/74
            * 需要在BIOS中开启CPU的虚拟化功能，可以在任务管理器的性能面板的CPU部分看到是否开启成功
* Docker Hub
* Docker CE和Docker EE
    * Docker从17.03开始分为企业版与社区版，社区版并非阉割版，而是改了个名称；企业版则提供了一些收费的高级特性。
    * Docker Enterprise
* Docker Engine
* 使用
    * 获取/制作镜像
        * 可以从Docker Hub等地方下载镜像：可以通过命令Docker pull xxx下载
        * 可以自己制作一个镜像
    * 使用镜像创建/运行容器
* 命令
    * 运行容器：docker run -it 镜像名 /bin/bash
    * 退出容器：exit 或者 Ctrl+P+Q
    * 查看容器：docker ps -a
    * 查看运行的容器：docker ps
    * 重启容器：docker restart 容器ID
    * 重启容器后进入交互式：docker start -i 5c6ce895b979
    * 进入容器：docker attach 容器ID 或者 docker exec -it 容器ID /bin/bash