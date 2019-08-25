* 概述
    * 类似ant和maven，简单来说是一个构建工具，高级使用的话也可以作为一个基于Groovy语言的编程框架，所有配置其实都是code。
    * 与maven、ant
        * gradle有点类似于ant，提供了很多task，但是又更加方便（尤其装了eclipse的插件后，可以直接双击触发task，这一点也类似于maven，其在IDEA上也可以直接双击触发），因为有许多现成的插件提供了许多现成的task可以用，比如spring-boot等插件。
        * 如上所述，maven也有build等task的类似概念，但更倾向于/善于进行repo管理
* 参考
    * [Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/)
    * [Gradle史上最详细解析](https://www.cnblogs.com/wxishang1991/p/5532006.html)
    * [十分钟理解Gradle](https://www.cnblogs.com/Bonker/p/5619458.html)
    * [基于Gradle的Spring Boot项目构建](https://blog.csdn.net/xingyuzhe/article/details/80346467)
    * [用Gradle构建Spring Boot项目](https://www.cnblogs.com/davenkin/p/gradle-spring-boot.html?utm_source=itdadao&utm_medium=referral)
* 概念
    * **gradle wrapper / gradlew**
            * gradlew和gradlew.bat是生成的gradle wrapper在Linux和Windows上的脚本，使用它们就可以统一大家用的gradle版本了，而且**就不需要手动安装和配置了，但是还是会自动下载到user目录下的.gradle的wrapper目录下**
            * 这里的gradlew和gradlew.bat其实只是脚本文件（前者用于Unix/Linux/Mac，后者用于Windows），在使用gradle命令的地方替换为gradlew（Linux上用./gradlew）或gradlew.bat，他们将自动下载指定的gradle版本，然后用该版本进行项目构建。
            * **每个项目下面都有自己的gradlew/gradlew.bat，最终用的是gradle/wrapper/下面的gradle-wrapper.jar，版本信息在gradle/wrapper/gradle-wrapper.properties中，**
            * 可以使用gradlew taskname
    * **每个build.gradle都是一个gradle脚本文件，看起来像是配置文件，但其实都是基于Groovy语言的脚本。**
        * 每个大括号外面的taskname等其实都是函数名，大括号里面是参数。implementation后面跟空格和一个单引号包裹的字符串，其实也就是一个函数调用及其参数。
    * Project：每个项目可作为一个Project，对应一组配置文件，有一系列task。
    * Task：每个插件对应会有一系列预定义的task，如bootRun、build之类的。
    * 插件：每个Project可以使用一些插件，比如Android的插件、Spring Boot的插件、普通Java的插件，每个插件对应一系列预定义的task，一个Project可以用多个插件。
* gradle.properties配置文件
    * 可以配置一些变量及值，然后用在build.gradle配置文件中
    * 可以配置代理
    * **和版本无关，user目录下的gradle配置文件优先级比项目目录下的高**
    * 只不过有的建的工程在根目录下有这个配置文件，有的没有？
* 工作流程
    * 首先是初始化阶段。对我们前面的multi-project build而言，就是执行settings.gradle
    * Initiliazation phase的下一个阶段是Configration阶段。
    * Configration阶段的目标是解析每个project中的build.gradle。比如multi-project build例子中，解析每个子目录中的build.gradle。在这两个阶段之间，我们可以加一些定制化的Hook。这当然是通过API来添加的。
    * Configuration阶段完了后，整个build的project以及内部的Task关系就确定了。恩？前面说过，一个Project包含很多Task，每个Task之间有依赖关系。Configuration会建立一个有向图来描述Task之间的依赖关系。所以，我们可以添加一个HOOK，即当Task关系图建立好后，执行一些操作。
    * 最后一个阶段就是执行任务了。当然，任务执行完后，我们还可以加Hook。
* 使用命令
    * 可以在project目录下使用gradle taskname来执行某个task，即使是bootRun也可以
    * gradle tasks查看任务信息
    * gradle projects查看工程信息
* 在Eclipse/STS中使用Gradle
    * 集成Gradle的功能其实主要靠的是buildship这个Eclipse插件
    * 不管是导入还是新建的项目，**一定要有build.gradle等文件并且和使用的Gradle版本一致**，才能通过插件识别为Gradle项目，即才能在Gradle Tasks和Gradle Execution窗口看到东西，并且buildpath等是正确的。
* 项目目录下的文件
    * gradlew和gradlew.bat：如前面所述
    * settings.gradle
        * 多项目时，父目录会有一个settings.gradle文件
            * 可以通过rootProject.name = 'xxx'设置根项目的名称
            * 可以通过include 'yyy' project(':yyy')        projectDir = new File(settingsDir, '../../shared/yyy')指定有哪些子项目
    * build.gradle
        * 定义group和version：group = 'com.xxx.yyy'        version = '0.0.1-SNAPSHOT'
        * 指定使用什么插件，可以有多个，如apply plugin: 'org.springframework.boot'
        * ext用于定义变量？
        * 指定依赖项：
    * gradle.properties（可能有，如前面所述）

```
dependencies {
	compile('org.springframework.boot:spring-boot-starter-web')
	compile('org.springframework.boot:spring-boot-starter-hateoas')
        compile('org.springframework.boot:spring-boot-starter-cache')
	compile('org.springframework.cloud:spring-cloud-cloudfoundry-connector')
        compile  project(':XXXCommon')
        compile  project(':SwaggerSupport')
	
	compileOnly('org.projectlombok:lombok')
	
	testCompile('org.springframework.boot:spring-boot-starter-test')
	testCompile('org.testng:testng:6.8')
}
```

* 项目间引用
    * 如平级项目间引用
    * 同一插件只需要且必须要在顶层指定版本。

```
// 被引用项目的settings.gradle
rootProject.name = 'DataBaseDriver'

// 被引用项目的build.gradle
plugins {
	id 'org.springframework.boot' //version '2.1.4.RELEASE'
	id 'java'
}


// 引用项目的settings.gradle
rootProject.name = 'BackendApp'
includeFlat 'DataBaseDriver'

// 引用项目的build.gradle
plugins {
	id 'org.springframework.boot' version '2.1.4.RELEASE'
	id 'java'
}

dependencies {
	compile project(':DataBaseDriver')
}
```

* 单元测试
    * 根项目目录的build.gradle文件中可以放统一的单元测试的配置
        * 或者引用另一个子的专门的配置文件，如test.gradle，只需要加上：
```
if ( project.name != "AMP") {
	apply from: file('./../../test.gradle')
}
```
    * 每个项目的build.gradle配置文件中需要添加单元测试相关的依赖：
```
dependencies {
	testCompile('org.springframework.boot:spring-boot-starter-test')
	testCompile('org.testng:testng:6.8')
}
```
    * **spring boot插件的build task中已经包含了单元测试test**
    * jacoco是覆盖率插件
    * 可以添加新的测试框架如testng，单元测试中的@Test引用testng包下的，还可以指定testing的task在哪个task之前执行，如：

```
apply plugin: 'jacoco'

jacoco{
	toolVersion = "0.8.1"
	reportsDir = file("${buildDir}/reports/jacoco")
}

task testNG(type: Test) {
	useTestNG {}
	reports.html.destination = file("$buildDir/reports/testngReport")
					
	jacoco {
		append = false
		destinationFile = file("$buildDir/reports/jacoco/testNG.exec")
	}
}

check.dependsOn testNG
```

* 与Maven的关系
    * 关系
        * [Gradle使用Maven仓库的方法](https://www.jb51.net/article/135211.htm) 
        * **gradle和maven类似，也有仓库的概念**
        * **下面对mavenLocal()、mavenCentral()的解释可能错了。可能是：如果repositories {}中写的是mavenCentral()，那么说明用的远程中心仓库是官方的，或者在maven的配置文件中配置的仓库；如果写的是mavenLocal()，那么说明用的是自己指定的公司或个人的私服，当然也会优先查找本地缓存仓库**
        * gradle也可以使用maven的repo功能，可以在项目根目录的build.gradle文件中使用，如下配置（配了以后优先从maven中下载依赖包）。
            * **仅使用mavenLocal()说明会用本地user目录.m2文件夹下的本地仓库缓存（当前也可能修改了目录）；仅使用mavenCentral()说明gradle只是使用了maven的远程仓库，下载下来的包不会放到.m2中，而是按gradle的仓库缓存结构放到用户目录的.gradle目录下的缓存中。**
            * **然后在dependencies中增加maven写法的依赖**。如 compile group: 'org.apche.lucene', name: 'lucene-core', version: '3.6.0'
                * **可以在[mvnrepository官方网站](https://mvnrepository.com/)上看到某个包的Maven、Gradle等各种使用方式下的地址**

```
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
	
	buildscript {
	
		dependencies {
	    	classpath 'io.spring.gradle:dependency-management-plugin:1.0.5.RELEASE'
	   	}
		repositories {
	
			mavenLocal()       //（直接使用）本地库。可能可以配本地maven repo目录？Spring tool suite估计是使用的内置的maven插件和本地地址
			
			//mavenCentral()  //中央/远程库
			
			maven {               //替代mavenCentral()，直接通过URL指定中央/远程库的URL和认证信息
		        url virtualRepoUrl
		        credentials {
		            username artifactory_user 
		            password artifactory_password 
		        } 
		    }
		}
	}
```

* gradle根项目目录下的build.gradle文件中，可能会指定maven仓库
    * 目录
        * Gradle在自己的.gradle目录中（Linux下在/home/.gradle下），会有配置文件（其中的代理、认证信息也可以在配在Jenkins界面上，然后构建脚本中读取到后直接动态写到gradle.properties文件中）、缓存文件等。
            * gradle的缓存目录在C:\Users\z003xxxe\.gradle\caches\modules-2\files-2.1下
            * gradle的插件、task也可能会用到maven
        * Maven的repo目录在.m2目录下，如C:\Users\z003xxxe\.m2\repository
        * **Gradle仓库/缓存目录结构与Maven仓库不同**
            * 不同
                * gradle缓存通常把报名作为第一级目录，然后里面按版本再放jar包，如\org.springframework.boot\spring-boot-starter\1.5.14.RELEASE；而maven是把包名拆分后作为多级目录，如\org\springframework\boot\spring-boot-starter\1.5.14.RELEASE。
                * 在上述目录下，maven通常直接就是jar、pom等文件了，而gradle会再多一级编码的目录，可能是和机器或者用户相关？如C:\Users\z003xxxe\.gradle\caches\modules-2\files-2.1\org.springframework.boot\spring-boot-starter\1.5.14.RELEASE\877545b5a85905a925e90e5fb41c2e72e985ac54\spring-boot-starter-1.5.14.RELEASE.pom
            * 如果遇到网络不通等问题，需要把开发环境的gradle缓存文件放到maven repo中给编译用时，需要转换目录结构。可参考[gradle cache转为local Maven库，解决gradle编译依赖包时常被墙](https://blog.csdn.net/lzf_china/article/details/42965403)，[gradle缓存库转成maven](https://blog.csdn.net/gcola007/article/details/83313434)
                * **把脚本放到C:\Users\z003xxxe\.gradle\caches\modules-2\files-2.1目录下后Python运行，可以配想要转换当前目录下的哪些文件夹**
                * **只能转xxx.xxx.xxx这种目录，commons-codec要手动转**

* 使用ant
    * 可以在自定义的task中使用ant的功能/target

```
task wsimport {
	ext.sourcesDir = "${buildDir}/generated-sources/wsdl"
	ext.classesDir = "${buildDir}/classes/wsdl"
	ext.wsdlSrc = file('src/main/resources/message.wsdl')
	ext.customBindingSrc = file('src/main/resources/CustomBinding.xml')

	outputs.dir classesDir

	doLast {
		project.ant {
			mkdir(dir: sourcesDir)
			mkdir(dir: classesDir)

			taskdef(name:'wsimport',
					classname:'com.sun.tools.ws.ant.WsImport',
					classpath:configurations.jaxws.asPath)

			wsimport(keep:true,
					destdir: classesDir,
					sourcedestdir: sourcesDir,
					wsdl: wsdlSrc,
					extension: true,
					binding: customBindingSrc)

		}
	}
}
```

* 使用wsdl文件自动生成使用webservice的代码，并导入buildpath
    * eclipse中需要在build后，重新执行gradle任务ide->cleanEclipse或ide->eclipse，否则还是无法使用自动生成的类

```
configurations {
	jaxws
}

// tag::wsdl[]
task wsimport {
	ext.sourcesDir = "${buildDir}/generated-sources/wsdl"
	ext.classesDir = "${buildDir}/classes/wsdl"
	ext.wsdlSrc = file('src/main/resources/message.wsdl')
	ext.customBindingSrc = file('src/main/resources/CustomBinding.xml')

	outputs.dir classesDir

	doLast {
		project.ant {
			mkdir(dir: sourcesDir)
			mkdir(dir: classesDir)

			taskdef(name:'wsimport',
					classname:'com.sun.tools.ws.ant.WsImport',
					classpath:configurations.jaxws.asPath)

			wsimport(keep:true,
					destdir: classesDir,
					sourcedestdir: sourcesDir,
					wsdl: wsdlSrc,
					extension: true,
					binding: customBindingSrc)

		}
	}
}
// end::wsdl[]

compileJava {
	options.compilerArgs << '-Xlint:deprecation'
	dependsOn wsimport
	source += wsimport.ext.sourcesDir
}

dependencies {
	compile(files(wsimport.classesDir).builtBy(wsimport))
	jaxws('com.sun.xml.ws:jaxws-tools:2.1.7')
}
```

* 经验
    * 如果有多个项目，最好上级根目录有一个总的build.gradle，这样的话可以有一个统一的入口，方便编译所有project或者一次性导入eclipse

* 其他
    * Gradle和Maven都可以使用**JFrog Artifactory**作为中心仓库，可以集成域账户登录，对应前面配置文件中的artifactory_user、artifactory_password。主页为https://captain.rtf.xxx.xxx/artifactory/webapp/#/home，也可以直接用路径访问仓库目录（rtf格式？）。
    * 一些框架如Spring Boot的不同版本可能会需要指定版本以上的Gradle
    * 没有使用Spring Boot等插件的java的gradle项目，在build时输出的jar文件可能没有包含依赖的jar包，需要自定义task来实现，并且只能用命令执行task，Spring Tool Suite中有可能并没有把这个task列出来：
```
task fatJar(type: Jar) {
	        manifest {
	    }
	    baseName = project.name + '-all'
	    from { configurations.compile.collect { it.isDirectory() ? it : zipTree(it) } }
	    with jar
	}
```