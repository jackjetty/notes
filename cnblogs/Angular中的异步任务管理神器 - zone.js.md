* [zone.js - 暴力之美](https://www.cnblogs.com/whitewolf/p/zone-js.html)
* JavaScript是单线程执行的，因此为了不阻塞UI界面的用户体验，在JavaScript执行的很多耗时操作都被封装为了异步操作，如：setTimeout、XMLHttpRequest、DOM事件等。
* zone.js为JavaScript提供了执行上下文，可以在异步任务之间进行持久性传递。zone.js采用猴子补丁（Monkey-patched）的暴力方式将JavaScript中的异步任务都包裹了一层，使得这些异步任务都将运行在zone的上下文中。每一个异步的任务在zone.js都被当做为一个Task，并在Task的基础上zone.js为开发者提供了执行前后的钩子函数（hook）。
    * 有时在分析前端性能问题时，如果使用了浏览器的performance功能，在看call tree时，会发现zone.js中的回调函数占用了很多时间，那么就要分析一下是否是必须的了。