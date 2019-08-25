* 页面上的异步ajax请求、js等文件的异步加载，可能会遇到cookie中存的session超时的情况，这是服务端/平台可能会返回302响应码要求客户端（browser）跳转到登录页。
* 302 响应
    * Response Header中会有一个location字段，是要求客户端（browser）跳转到的地址，如登录地址。
    * 前端代码（JavaScript或者Angular等框架中的intercept等过滤器）无法捕捉到302响应，因为302响应是browser处理的，并不会冒泡给JavaScript去处理。因此如果脚本中想要判断是不是异步请求遇到了session超时的情况，可能要捕捉下一个browser根据302响应及其中的location字段自动发起的到目标location的请求的200的响应，然后判断这个地址是不是登录地址。