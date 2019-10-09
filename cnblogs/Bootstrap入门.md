* 资源
    * [中文官网](https://www.bootcss.com)
    * [英文官网](https://getbootstrap.com)
* 使用cdn地址引入Bootstrap
    * <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
* 全局CSS样式
    * [全局 CSS 样式](https://v3.bootcss.com/css/)
        * HTML5 文档类型。Bootstrap 使用到的某些 HTML 元素和 CSS 属性需要将页面设置为 HTML5 文档类型。在你项目中的每个页面都要参照下面的格式进行设置。

```
<!DOCTYPE html>
<html lang="zh-CN">
  ...
</html>
```

        * 移动设备优先。为了确保适当的绘制和触屏缩放，需要在 <head> 之中添加 viewport 元数据标签。
            * <meta name="viewport" content="width=device-width, initial-scale=1">
            * user-scalable=no
                * 在移动设备浏览器上，通过为视口（viewport）设置 meta 属性为 user-scalable=no 可以禁用其缩放（zooming）功能。这样禁用缩放功能后，用户只能滚动屏幕，就能让你的网站看上去更像原生应用的感觉。注意，这种方式我们并不推荐所有网站使用，还是要看你自己的情况而定！
                * <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
        * Normalize.css
            * 为了增强跨浏览器表现的一致性，我们使用了 Normalize.css，这是由 Nicolas Gallagher 和 Jonathan Neal 维护的一个CSS 重置样式库。
* 布局容器与栅格系统