* Spring Boot支持jar包、war包两种形式的部署
    * 如果用的是maven，默认应该是jar的打包方式，如果要打包成war，需要做一些代码和pom.xml文件的修改
    * 如果用的是gradle，直接就有jar和war及相关的task。
        * 直接在build.gradle中指定使用jar、war插件即可？然后默认的打包方式就变了？
* 在STS或者Eclipse中配置Server（Tomcat）
    * 在Server标签页或者Window->Perferences->Server->Runtime  Environment中增加适当版本的Tomcat Server，并制定安装目录和使用的jre/jdk，然后就可以在Server标签页中看到该Server了。
    * 双击该Server
        * Overview页面
            * Server Locations
                * 必须把publish到该Server的项目都删掉，并且clean，状态变成Synchronized，才能修改
                * 如果要修改使用本地安装的Tomcat而不是STS或Eclipse自带的Tomcat，那么要选第二项，并修改Deploy path，一般是webapp，而不是内嵌的Tomcat的默认的wtpwebapp。
                * ![Eclipse Server Configuration](https://img2018.cnblogs.com/blog/106125/201905/106125-20190510154453835-1038695036.png)
        * Modules页面
            * 可以修改访问某项目的初始路径，如果要让该项目可以从根路径访问，一定需要命名为ROOT并把Path改成/？（如NB）
    * 在该Server上右击选择"Add and Remove..."可以增加或者删除需要部署到该Server上的项目
        * 修改后，需要点clean、publish才生效，然后再start
* 怎么解决Gradle的Spring Boot项目部署到独立的Tomcat，并可以从根路径访问？
* Spring Boot项目打包成jar或者war，都有内嵌的Tomcat？后者怎么运行或部署？