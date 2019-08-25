* 发送http请求的工具
* 特性
    * 在客户端或者网站上都可以操作
    * 支持https
    * 支持设置代理
    * 支持设置每次请求的Header、Param、Authorization（其实也是Header的一部分）
    * 会自动保留每次的请求设置和记录
        * 勾选后也可以自动保留每次的响应
    * 支持以collection为单位对接口访问记录进行命名、组织、保存、导出（json格式）、导入、自动化测试、动态调用接口。
    * **支持注册免费账号，并同步Collection等信息。**
    * **工具右侧窗口的"code"部分可以查看当前request对应的curl、各种语言等命令或代码，profile网页上也可以看**
    * 可以显示请求或响应的纯文本或pretty后的样子。
    * 可以模拟浏览器发起请求
        * 需要点击右侧的Cookies文件链接，在弹出窗中设置各个domain下的所有Cookie
            * 好像要一个一个的save
* 概念
    * workspace
        * 分为Personal workspace和Team workspace两种
            * 每个个人账号有个默认的 personal workspace - "My Workspace"
            * 每个team有个默认的 team workspace - "Team Workspace"
            * 可以在顶部切换到personal workspace或team workspace
                * ![切换workspace](https://img2018.cnblogs.com/blog/106125/201906/106125-20190618155533912-1042603294.png)
        * 每个workspace可以有多个collection
        * 每个workspace可以有多个environment，每个environment包含一组变量及值
    * team
        * 每个team可以有多个人
        * 每个team可以有多个workspace（默认的"Team Workspace"好像删不掉，功能类似Personal的"My Workspace"）
    * environment
        * 每个environment可以有多个key-value键值对，即每个environment包含一组变量
        * 由于同一套接口可能会对应多个环境（如dev、test、prod）而导致请求地址或参数不同，因此需要定义一组变量，然后这组变量在不同的environment中值不同。因此自然每个environment中的对应的变量的key名称肯定是相同的，只是在不同的environment中值不同。
        * 还可以定义一些Global的变量
        * 可以共享到personal workspace或者team workspace
        * 可以download成json文件，可以import
        * **environment中的key名称对应request的url或者参数中的占位符{{var_name}}中的var_name**
        * 可以切换或者管理当前workspace的environment，并且share、download、export、edit environment的操作都在这个管理弹窗中
            * ![管理environment](https://img2018.cnblogs.com/blog/106125/201906/106125-20190618155740984-2047388430.png)
    * collection
        * 一组request的集合
        * 可以命名collection，可以share，可以export成json文件
    * APIs
        * 新功能，貌似是用于接口设计的，可以选择接口符合的规范（如Open API 3.0）
        * 以一个一致的模式定义了相关的collections和environments
        * 有自己的版本管理机制？
* 使用**Collection Runner**动态发起多次调用，每次都动态拼接URL、参数等
    * 即使用Postman的**Collection Runner**功能，在工具栏有一个“Runner”按钮。
        * Collection Runner功能是以Collection为单位执行的，所以每次这个Collection里面的接口都会跑
        * 先在url等处的动态参数位置标明参数名，如{{parameter}}
        * 在Pre-request Script标签页中设置url等处的参数与参数文本文件中参数的映射关系，如pm.environment.set("parameter",data.parameter);
            * **这几个地方的参数名必须都一致，包括大小写**
        * 点击工具栏的Runner按钮
        * 选择Collection
        * 设置Iterations参数，指调用多少次接口，即后面使用的参数文本文件中有多少个动态参数
        * 选择参数文本文件（文本文件中第一行是参数名，多个的话用逗号隔开，后面每一行是一组参数）
        * 选择Data File Type，一般是text/csv
        * 最后点Run xxx
        * 执行完成后还可以导出报告
    * ![postman_collection_runner_1.png](https://img2018.cnblogs.com/blog/106125/201907/106125-20190730150613425-1600433554.png)
    * ![postman_collection_runner_2.png](https://img2018.cnblogs.com/blog/106125/201907/106125-20190730150706225-178441820.png)
    * [postman 读取本地文件作为参数](https://blog.csdn.net/qq_36350532/article/details/81867828)
* As we are involved in more and more web-based projects such as MindSphere related projects, we need more powerful test tools to test our HTTP APIs or MindSphere platform API, etc.
    * Postman (there is a free version and it’s enough)
        * A GUI tool to send http request.
        * Available both on Linux and Windows, but we usually use it on Window.
        * [Download Page](https://www.getpostman.com/downloads/)
        * Useful functions
            * You can configure a request’s method, url, params, headers, etc. And you can pretty the response content such as using Json format.
                * ![postman_basic.png](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306161649802-1437915717.png)
            * Auto save sent request
                * ![postman_hisroty.png](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306161711882-1914580805.png)
            * Can name, organize and save sent requests in a collection.
                * ![postman_collection.png](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306161734203-1921005583.png)
            * Can export saved requests, share to other one and import it.
                * ![postman_export.png](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306161759686-1822672732.png)
            * Can set proxy server
                * ![postman_proxy.png](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306161814637-723053742.png)