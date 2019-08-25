* Spring Boot也支持MVC
* 部署
    * 一般打包成war进行部署？一样可以使用Gradle
    * 如果STS要使用本地安装的Tomcat，和以前Eclipse做法类似，添加server，把项目添加上去，双击进行配置
* application.properties配置
    * 指定jsp文件位置
        * spring.mvc.view.prefix=/WEB-INF/jsp/
        * spring.mvc.view.suffix=.jsp
* controller
    * 接口返回的数据类型是ModelAndView
    * ModelAndView有addObject等方法可以以key/value方式传送数据给jsp页面文件（view）
* jsp文件
    * 放在/src/main/webapp/WEB-INF/jsp下面，部署后WEB-INF也会放置class文件
    * 页面上数据变量表达式的数据替换发生在服务端，js的动态渲染发生在客户端，使用表达式的渲染呢？
    * js通过表达式${Report}获取controller传给的数据进行页面渲染（用表达式或者直接JavaScript的document.write()方法）
    * 如果需要和服务端交互，就再用js进行ajax通讯