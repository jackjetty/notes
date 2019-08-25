* CSS3
    * 支持使用**calc()**计算高度
        * 使用通用的数学运算规则，但是也提供更智能的功能
            * 使用“+”、“-”、“*” 和 “/”四则运算；
            * 可以使用百分比、px、em、rem等单位；
            * 可以混合使用各种单位进行计算；
            * 表达式中有“+”和“-”时，其前后必须要有空格，如"widht: calc(12%+5em)"这种没有空格的写法是错误的；
            * 表达式中有“*”和“/”时，其前后可以没有空格，但建议留有空格。
    * viewport：可视窗口，也就是浏览器。
        * vw：Viewport宽度，1vw 等于viewport宽度的1%
        * vh：Viewport高度，1vh 等于viewport高的的1%，100vh即代表100%的高度，可以直接和px像素数进行加减计算
            * 如height: calc(100vh - 44px - 80px - 30px - 16px - 46.6px - 190.5px);
* @media媒体查询
    * [CSS3 @media 查询](https://www.runoob.com/cssref/css3-pr-mediaquery.html)
    * 使用 @media 查询，你可以针对不同的媒体类型定义不同的样式。
    * @media 可以针对不同的屏幕尺寸设置不同的样式，特别是如果你需要设置设计响应式的页面，@media 是非常有用的。
    * 当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。
    * 媒体类型指用于做判断的媒体，包括screen、print、all等
    * 媒体功能指用于做判断的选定的媒体的某个属性，包括max-width、height、resolution、orientation等
    * Bootstrap的栅格系统就是基于@media媒体查询实现的
* CSS3和@media媒体查询结合
    * 常采用递进的方式，类似于给定一个默认样式，然后根据媒体功能进行一级级的判断。如下面的例子，其实这些属性都会交给浏览器处理，只不过浏览器只让符合条件的一个生效。
    * 如果项目的样式表用的是less语法，那么就可以直接用Bootstrap的规格如width: @container-sm;，但如果用的是css或者scss，那么就不行了，只能用Bootstrap各规格对应的像素数
    * 768px、1200px通常对应常见的分辨率，也通常和Bootstrap中的xl等规格划分相对应

```
    height: 300px;
    @media screen and (max-width: 767px) {
        height: calc(100vh - 44px - 80px - 30px - 16px - 46.6px - 190.5px);
    }
    @media screen and (min-width: 768px) {
        height: calc(100vh - 44px - 80px - 30px - 16px - 46.6px - 128px);
    }
    @media screen and (min-width: 1200px) {
        height: calc(100vh - 44px - 80px - 30px - 16px - 46.6px - 121px);
    }
```