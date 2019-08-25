* 概述
    * 为了避免重复手动或用IDE生成getter、setter
    * lombok其实应该是在编译时才生效，即才会生成getter、setter代码，因为了避免在IDE中编译前报错，需要安装插件，来帮忙去掉错误显示（可能只是帮IDE去分析了一下，或者是生成了一下）
* 安装Eclipse插件
    * 增加依赖
        * maven：先要在pom.xml文件中增加依赖（可以不指定版本，eclipse会自动选择）

```
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```

        * gradle：dependencies中增加compileOnly('org.projectlombok:lombok')
    * 然后在下载的lombok.jar依赖包目录运行java -jar lombok.jar以打开安装界面，选择eclipse安装目录后安装即可
        * sts4可能需要新版的lombok才行，而且有可能需要和项目中依赖的版本基本一致才行
    * 安装完成后要重新maven update project。**而且要重启IDE！！！**
* 在代码中使用
    * 使用时，需要在类或者成员上标志@Data、@Getter、@Setter
    * 还可以使用@NoArgsConstructor、@ToString等注解
* 可以有单独的配置文件lombok.config

```
lombok.addLombokGeneratedAnnotation = true
```

* 注意
    * 如果报错“getId() is undefined”，可能是虽然增加了依赖，但没有安装lombok.jar插件，即参考上一项安装。也有人说是eclipse太新，也有人说需要打开eclipse的注解开关什么的。