* 可以简单理解为命令行版的Postman
* Linux和Windows上都有
* [Windows版下载](https://curl.haxx.se/windows/)
    * 解压后，把目录放到path环境变量中，就可以在cmd中使用curl命令了
* 使用
    * 可以通过参数指定HTTP头部、请求参数等
* As we are involved in more and more web-based projects such as MindSphere related projects, we need more powerful test tools to test our HTTP APIs or MindSphere platform API, etc.
    * Curl (free and open source)
        * A command line tool to send http request.
        * Available both on Linux and Windows (should add the unpacked bin path to the “Path” environment variable).
        * [Download Page](https://curl.haxx.se/download.html)
        * Support many parameters, such as:
            * Use “-H” to specify HTTP headers
            * Use “-c” to store cookies from server
            * Use “-X” to specify HTTP method, default GET
        * Also support other protocols such as LDAP an so on.
        * ![](https://img2018.cnblogs.com/blog/106125/201903/106125-20190306154323386-143265888.png)