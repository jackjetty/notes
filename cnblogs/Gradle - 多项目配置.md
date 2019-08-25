# 1. 理论
* 不管是根项目还是子项目的，**build.gradle文件**定义了使用的插件、仓库、依赖等真实有用的配置。**settings.gradle文件**纯粹是用来定义根项目和子项目及关系的，如项目名，根项目、子项目的关系，以及子项目的目录、gradle文件位置等信息。
* 根据根项目和子项目的目录关系，可以分为**分层布局**与**扁平布局**
    * 分层布局：推荐。没有一个单独的根项目目录，根项目是指虚拟的概念，根项目的build.gradle及settings.gradle文件就在根目录，和子项目的根目录平级。
    * 扁平布局：根项目也有一个单独的目录，和其他子项目平级，根项目的build.gradle及settings.gradle文件在根项目文件夹下面。
* 根项目和子项目的gradle配置，可以只用根项目的build.gradle及settings.gradle文件，也可以拆分成根项目引用各个子项目的build.gradle及settings.gradle文件。

# 2. 父子项目的具体配置
* 根项目的build.gradle
    * 通过后面三种级别的配置可以把子项目的配置都放在根项目的build.gradle中，当然最好拆开。
    * 可以通过allprojects进行所有根项目和子项目的通用设置
    * 可以通过subprojects进行所有子项目的通用设置
    * 可以通过project(':projectname')进行特定子项目的设置

```
allprojects {
	group = 'xxx'
	version = '0.1'
}

subprojects {
	apply plugin: 'java'
	apply plugin: 'eclipse'
}

project(':Common') {
	dependencies {
            compile project('XXX')
    }
}

if ( project.name != "Root") {
	apply from: file('./../../test.gradle')
}
```

* 根项目的settings.gradle
   * 可以设置本根项目的项目名，如：rootProject.name = 'Root'
   * 可以配置引用了哪些子项目，如：
        * 如果是分层布局，则使用include 'projectname'来引入子项目依赖
        * 如果是平面布局，则使用includeFlat 'projectname'来引入子项目依赖

```
include 'Common'
project(':Common').projectDir = new File(settingsDir, 'shared/Common')
```

    * 配置子项目时，还可以自定义build.gradle文件的名称，如：

```
include 'shared:'Common'
project(':shared:Common').buildFileName = 'Common.gradle'
```

* 子项目的build.gradle
    * 可以通过buildscript引用其他的build.gradle文件，如：

```
buildscript {

	apply from: file('./../../build.gradle')
	ext {
		springBootVersion = '1.5.14.RELEASE'
	}
}
```

    * 直接进行本子项目的配置，如group、version、apply plugin:、dependencies、dependencyManagement、jar等。
* 子项目的settings.gradle
    * 通常只设置本子项目的项目名或者build.gradle文件名，如：

```
rootProject.name ='Common'    
rootProject.buildFileName ='Common.gradle'
```