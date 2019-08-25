* 在Eclipse中通常会把依赖的jar包放到JRE System Library（项目设置的jdk基础库）或者Project and External Dependencies（gradle等依赖管理工具管理的库）下面
* 可以在项目右击选择Build path，或者JRE System Library和Project and External Dependencies来看依赖的jar及其attach的源码情况
* 如果想在Eclipse中看到使用的jar包的源码，那么需要
    * 引用的直接就是源码的jar包
        * 比如通过gradle引入的库
    * 引用的是编译后的包含class文件的jar包，但另外attach了对应的源码（包）
        * 比如依赖的jdk，会自动设置好Java Source Attachment中的External location，如C:/Program Files/Java/jdk1.8.0_201/src.zip
        * 如果是其他的包，就要在依赖的jar包上右击，手动设置用来attach的源码包
        * 如果没有attach源码包，那么就看不到源码，但是Eclipse也会帮忙解析出包结构显示在树上