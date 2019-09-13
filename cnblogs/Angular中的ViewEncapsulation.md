* Angular的ViewEncapsulation即封装模式
    * Native
        * 原先浏览器 Shadow DOM 行为
        * Native其作用是让组件样式不进不出，彻底封闭，全局样式也无法修改内部样式；
        * 应该一般不会用，因为一般都想让外部的通用样式影响到内部，比如bootstrap的一些样式
        * Angular将为组件创建Shadow DOM。
        * **样式的范围是组件。**
    * Emulated
        * 仿真模式，通过 Angular 来模拟类似 Shadow DOM 的行为，**angular的默认方式**
        * Emulated，其作用是让组件的样式只进不出，换言之即组件内的样式不会影响到外部组件；
        * 也就是说默认情况下，Angular内部component的样式并不会影响到上层，也就是不能操作上层的样式。编译后，不会把样式带上去。
        * Angular不会为该组件创建Shadow DOM
        * **样式将被限定于组件**
        * **这是封装的默认值。**
    * None
        * 无任何封装行为
        * None方式相当于，组件内部定义的样式都是又进又出，相对于定义的全局样式。（非特殊情况慎用）
        * 可用于希望内部样式可以影响到外部时，或者说**想在内部component中操作外部的样式时**
        * 没有Shadow DOM。
        * **样式不在组件范围内。**
* 根本原理
    * [Angular中的ViewEncapsulation是什么？](https://baijiahao.baidu.com/s?id=1603683113292930767&wfr=spider&for=pc)
    * Angular框架为了组件之间的隔离，可能会给组件内的元素增加自动生成的属性，然后通过属性选择器限制该组件内属性的作用范围，如_nghost-c0、_ngcontent-c0等就是自动生成的属性，后面的序号用于区分不同的组件。**如果修改了ViewEncapsulation，那么这个行为会有所不同，比如设为none的话，就不会自动增加属性了**。
    * Shadow DOM与Angular中的ViewEncapsulation
        * Shadow DOM将 HTML 封装成HTML元素。使用Shadow DOM，标记，样式和行为被限制到元素，并且不会与DOM的其他节点发生冲突。Shadow DOM是Web Components的一部分，它封装了元素的样式和登录。
        * **Angular有它自己的模拟，它可以模拟Shadow DOM。为了模拟Shadow DOM并封装样式，Angular提供了View Encapsulation的类型。**
        * 理论上，Angular组件不是Web组件
    * Angular的component封装了web前端开发的底层细节，模拟成了桌面程序的样子，把每个component作为了一个整体，为了减少开发复杂度，默认单向隔离了component和上层之间的样式关联
    * 注意：**不要轻易修改component的ViewEncapsulation，因为多个开发人员进行开发时，容易不小心影响到外层样式。最好使用Emitter等方式让子组件可以修改父组件的变量，然后父组件在ngClass中用该变量来判断是否设置特定的样式class。**
* 修改某个component的ViewEncapsulation

```
import { ViewEncapsulation } from '@angular/core';

@Component({
  encapsulation: ViewEncapsulation.None
})
```