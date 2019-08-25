# 1. 安装
* 解压后，需要设置环境变量，通常先设一个MVN_HOME，然后Path中使用它，执行其bin目录
* Spring Tool Suite
    * 自带的Maven并不会在.m2目录生成settings.xml文件，但会有repository目录
    * 可以在Windows->Preference->Maven->Installation下面可以指定安装目录，使用自行下载安装的Maven，而不是STS内嵌的
    * 可以在Windows->Preference->Maven->User Settings下面设置使用哪个配置文件，默认是C:\Users\z003xxxe\.m2\settings.xml
* Settings.xml配置文件设置
    * 可以设置本地仓库地址
    * 可以设置使用的repository地址
    * 可以设置代理（含协议类型、认证信息等）
    * 例子
```
<?xml version="1.0" encoding="UTF-8"?>
<settings> 
<localRepository>D:\Tools\java\maven\repository</localRepository><!--需要改成自己的maven的本地仓库地址-->
    <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>
  <profiles>
    <profile>
       <id>nexus</id> 
        <repositories>
            <repository>
                <id>nexus</id>
                <name>local private nexus</name>
                <url>http://maven.oschina.net/content/groups/public/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
        </repositories>
        
        <pluginRepositories>
            <pluginRepository>
            <id>nexus</id>
            <name>local private nexus</name>
            <url>http://maven.oschina.net/content/groups/public/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile></profiles>
	<!--用来配置不同的代理，多代理profiles可以应对笔记本或移动设备的工作环境：通过简单的设置profile id就可以很容易的更换整个代理配置。  -->  
    <proxies>  
            
        <!--代理元素包含配置代理时需要的信息 -->  
        <proxy>  
                
            <!--代理的唯一定义符，用来区分不同的代理元素。 -->  
            <id>myproxy</id>  
                
            <!--该代理是否是激活的那个。true则激活代理。当我们声明了一组代理，而某个时候只需要激活一个代理的时候，该元素就可以派上用处。  -->  
            <active>true</active>  
                
            <!--代理的协议。 协议://主机名:端口，分隔成离散的元素以方便配置。 -->  
            <protocol>http</protocol>  
                
            <!--代理的主机名。协议://主机名:端口，分隔成离散的元素以方便配置。   -->  
            <host>127.0.0.1</host>  
                
            <!--代理的端口。协议://主机名:端口，分隔成离散的元素以方便配置。  -->  
            <port>31288</port>  
                
             <!--代理的用户名，用户名和密码表示代理服务器认证的登录名和密码 
            <username></username>   --> 
                
            <!--代理的密码，用户名和密码表示代理服务器认证的登录名和密码。 
            <password></password>   -->  
                
            <!--不该被代理的主机名列表。该列表的分隔符由代理服务器指定；例子中使用了竖线分隔符，使用逗号分隔也很常见。 -->  
            <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>  
                
        </proxy>  
           
    </proxies>  
</settings>
```

# 2. 常用命令
* mvn compile
* mvn test：编译并运行测试用例
* mvn spring-boot:run：运行spring-boot项目
* mvn package：打包项目
    * 会编译、运行测试用例、打包
    * 打包成pom文件中配置的格式，比如spring-boot默认打包成jar包，也可以打包成war包
* mvn clean：清除target目录下的内容（**常用于发生一些奇怪问题时，比如好像还在用旧代码**），可以和其他命令一起用，例如mvn clean package

# 3. 目录
* /src/main下面的java和resource目录是源代码和资源文件，打包时会被加入到最终的package中
* /src/test下面的文件在打包时不会被加入到最终的package中
* /target目录是编译、运行的临时文件；最终打包生成的文件；内容可被mvn clean清除；会自动创建该目录
* pom.xml是maven项目的配置文件

# 4. 依赖管理/解决过程
* [Maven依赖机制简介](https://www.cnblogs.com/tooy/p/7364149.html)
*[maven release版本不更新原因分析](https://blog.csdn.net/wangjun5159/article/details/77854572)
* 构建版本（pom文件中）
    * 具体版本
    * RELEASE
        * 如果版本号配置为RELEASE ，那么用本地MAVEN_REPOSITORY/groupid/artifactid/maven-metadata.xml，与服务器的maven-metadata.xml合并，会使用最大的release版本版本号。
        * 如果是release版本，首先从本地查找，如果有，则使用本地，否则从远程服务器下载。 因为，本地已存在，所以不会更新。所以，只有删除本地，才能更新到最新。 
    * SNAPSHOT
        * snapshot版本与release，略微不同，它比较的是lastUpdated，哪个新就下载哪个，所以如果版本号是x.x.x-SNAPSHOT，肯定会更新下来。
    * LATEST
        * 最新版本，则是发布版和快照中，最新的版本，所以，如果version配置为latest，则版本也不是稳定的。
* 传递性依赖
    * 假设你的项目依赖于一个库，而这个库又依赖于其他库。你不必自己去找出所有这些依赖，你只需要加上你直接依赖的库，Maven会隐式的把这些库间接依赖的库也加入到你的项目中。
    * 这个特性是靠解析从远程仓库中获取的依赖库的项目文件实现的。一般的，这些项目的所有依赖都会加入到项目中，或者从父项目继承，或者通过传递性依赖。
    * 传递性依赖的嵌套深度没有任何限制，只是在出现循环依赖时会报错。
    * 从上到下依次解决
* 依赖调解
    * “短路径优先”原则，意思是项目会选择依赖关系树中路径最短的版本作为依赖。当然，你也可以在项目POM文件中显式指定使用哪个版本。
* 依赖范围
* 排除依赖
* 可选依赖
* 其他
    * 如果某个上层组件依赖的不是具体版本的下层组件，比如依赖的是RELEASE版本，那么maven就会尝试去找最新的RELEASE版本，如果此时使用的仓库地址上没有版本列表元信息，那么有可能就会自动去下一个版本，比如上次用的是1.8.1，就会去找1.9.0，那么就会可能报错？？？

```
<?xml version="1.0" encoding="utf-8"?>

<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.sun.xml.stream.buffer</groupId>
  <artifactId>streambuffer</artifactId>
  <version>0.9</version>
  <dependencies>
    <dependency>
      <groupId>org.jvnet.staxex</groupId>
      <artifactId>stax-ex</artifactId>
      <version>RELEASE</version>
    </dependency>
    <dependency>
      <groupId>javax.activation</groupId>
      <artifactId>activation</artifactId>
      <version>1.1</version>
    </dependency>
  </dependencies>
</project>
```

# 5.中心仓库
* 可以使用[maven官方提供的仓库](https://mvnrepository.com/)，或者在中国的各个镜像源
    * **官方仓库中可以搜索组件，可以查看组件都有哪些版本，进入某一个版本的页面后，可以查看当前组件的pom文件，可以查看组件依赖哪些下一级组件，并且提供了maven、gradle等工具引用本包的写法**
    * 在gradle的build.gradle脚本中，**如果repositories {}中写的是mavenCentral()，那么说明用的远程中心仓库是官方的，或者在maven的配置文件中配置的仓库；如果写的是mavenLocal()，那么说明用的是自己指定的公司或个人的私服，当然也会优先查找本地缓存仓库**，也许还需要配置仓库和认证信息（$repo_user来自gralde.properties文件），如：

```
	ext {
		virtualRepoUrl                              = "https://captain.rtf.xxx.net/artifactory/xxx-gradle/"
	    
	    artifactory_user                            = "$repo_user"
	    artifactory_password                        = "$repo_encrypted_password"
	    
	}
	
	repositories {
	
		mavenLocal()
		
		//mavenCentral()
		
		maven {
	        url virtualRepoUrl
	        credentials {
	            username artifactory_user 
	            password artifactory_password 
	        } 
	    }
	}
```

# 6. 其他
* 和gradle类似，也有mvnw、mvnw.cmd用于免于独立安装maven并保证maven版本一致，把这两个作为Linux和Windows上的命令直接使用。
* 加个 -X参数可以显示更详细的信息
* 如果要手动下载jar包及其依赖的包，maven官网上就有，上面也列出了当前jar包的pom文件、jar包下载、依赖的包的链接
    * 但通常还是推荐使用maven、gradle去自动解决依赖下载jar包及需要的pom、xml文件，因为有可能间接依赖很多包，手动太麻烦
* Eclipse或者STS的输出窗口中可以切换到**Maven Console**，其中会输出Maven->Update等Maven命令过程中的信息。

# 7. 问题
* Spring Boot项目的pom.xml文件有时会报错：Error parsing lifecycle processing instructions
    * 可能是依赖冲突了，可以把maven仓库清空，重新更新一下项目。
    * 如果不是自己装的maven或者改过repository目录，那么通常是删除用户主目录下.m2/repository/下的所有依赖包。
* 有时STS或者Eclipse创建的jar打包方式的spring boot项目的**pom.xml文件的第一行会有错误，不显示错误信息或者显示Unknown**
    * 这是eclipse的maven的jar包打包插件的一个bug，貌似是2019年5月份才有的，根本原因是这个插件升级到了3.1.2版本后有bug，需要切换回3.1.1版本
    * **在pom.xml 文件中的 properties 加入maven jar 插件的版本号：<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>**
    * [SpringBoot 升级到 2.1.5.RELEASE 后 pom.xml 报 Unknown错误](http://www.wangwenhui.com.cn/archives/232)
    * [“unknown” error in pom.xml when I upgrade from Spring 1.5.21 to 2.1.5](https://stackoverflow.com/questions/56189383/unknown-error-in-pom-xml-when-i-upgrade-from-spring-1-5-21-to-2-1-5)
    * 指定插件版本并更新下载后，只要不清仓库，好像即使再建新项目，不指定，也不会报错了
* 如果网络有问题，那么在导入旧项目时，有可能无法下载相应版本的maven plugin的相关文件，会报错could not calculate build plan: plugin
    * 有必要的话需要设置正确的仓库地址，如使用阿里的国内仓库地址
    * 有必要的话需要设置正确的代理