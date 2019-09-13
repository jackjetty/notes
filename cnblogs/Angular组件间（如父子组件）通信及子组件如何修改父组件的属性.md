* 组件间通信
    * [官网-组件之间的交互](https://angular.cn/guide/component-interaction)
    * [Angular Pass data to parent component](https://www.tektutorialshub.com/angular/angular-pass-data-to-parent-component/)
    * [angular 组件通信的几种实现方式](http://www.manongjc.com/article/3386.html)
    * [Angular组件——父子组件通讯](https://www.cnblogs.com/starof/p/8636579.html)
    * **Angular中属性绑定是单向的，从父组件到子组件**
    * 父到子
        * 使用@Input()输入型绑定把数据从父组件传到子组件
        * 通过setter截听输入属性值的变化
        * 通过ngOnChanges()来截听输入属性值的变化
        * 父组件与子组件通过本地变量互动
        * 父组件调用@ViewChild()
    * 双向
        * 父组件和子组件通过服务+Rxjs来通讯
        * **使用EventEmitter让父组件监听子组件的事件**
            * **父组件和子组件通过EventEmitter来通讯**
                * 前提是父组件的html显式使用了子组件，比如app.component.html中，使用router-outlet标签结合app.routing.ts来引入不同的不确定的子组件，而没有直接具体写出某个子组件的标签，那就不行了。
                * 假设父组件是A，子组件是B且html名称为selector: 'b-template-list'，步骤如下：
                    * 子组件中创建有合适参数（要传递给父组件的数据类型）的用@Output修饰的EventEmitter，相当于C#中的事件：@Output() selectedTemplateData = new EventEmitter<ITemplate>();
                    * 子组件中根据业务需求，在合适的地方（如ngAfterViewInit、ngOnInit等生命周期函数或其他函数中）触发事件：this.selectedTemplateData.emit(null);
                    * 父组件中在其ts中定义子组件发过来事件后要触发的逻辑函数即回调函数，比如发生事件时要改变某个变量值：  onTemplateModeChanged(event) {this.templateMode = event;}
                    * 父组件中在其html中的子组件上增加事件、回调函数定义（其实回调函数的参数都写成$event，其实会被替换成子组件触发事件时传递的参数），将子组件的事件和父组件的回调函数联系起来，说明该子组件发出这类事件时触发父组件的该回调函数：<b-template-list id="b-template-list" (selectedTemplateData)='onTemplateSelected($event)'></b-template-list>
            * **父组件和子组件通过服务+EventEmitter来通讯**
                * [angular2/4通过服务实现组件之间的通信EventEmitter](https://segmentfault.com/a/1190000011425280)
                * 对于前面提到的某些父组件的html没有显式使用了子组件的情况，可以使用服务来传递EventEmitter
                * 步骤如下：
                    * 定义一个通用的服务，包含用于组件间通讯的多个事件

```
import { Injectable, EventEmitter, OnInit } from '@angular/core';

@Injectable()
export class EmitService {
    public clearContentMarginBottomEmit: EventEmitter<boolean>;

    constructor() {
        this.clearContentMarginBottomEmit = new EventEmitter(true);
    }
}

```

                    * 子组件中引入该服务，并在业务逻辑需要的时机触发事件

```
import { EmitService } from '../../shared/emit.service';

export class XXXComponent implements OnInit, OnDestroy, AfterViewInit {
  constructor(
    private readonly emitService: EmitService
}

  ngOnInit() {
    this.emitService.clearContentMarginBottomEmit.emit(false);
  }
}
```

                    * 父组件中使用该服务订阅该事件，并做相关业务处理

```
import { EmitService } from './shared/emit.service';

export class AppComponent implements OnInit {
  constructor(
    private readonly emitService: EmitService) {
  }

  ngOnInit() {
    this.emitService.clearContentMarginBottomEmit.subscribe((value: boolean) => {
      this.isClearContentMarginBottom = value;
    });
  }
}
```

* 关于ExpressionChangedAfterItHasBeenCheckedError
    * [关于ExpressionChangedAfterItHasBeenCheckedError](https://www.cnblogs.com/xudengwei/p/9214959.html)
    * 这是一个用于防止模型数据和ui之间数组不一致的一个警告机制，以便不让用户在页面上看到陈旧的或者错误的数据。
    * 该额外的检查只会在开发模式下执行，可能是因为一个不稳定的模型并不像框架产生的运行时错误那样严重。
    * 根本原因
        * 一个运行中的angular应用是一个组件树。
        * 在组件的生命周期内，angular有一个变更检测机制，会在进入一个新的生命周期阶段时检查新旧值是否一致。和ASP.NET有点类似？模拟桌面程序、服务器端控件模拟了组件状态？
        * 通常导致变化的罪魁祸首往往是组件或者指令。
        * 当然，真实场景的案例是错综复杂的，那些导致dom渲染的父组件属性更新或操作往往是通过服务或者可观察对象模式间接实现的，但是根本原理和原因是相同的。
    * 一些真实场景下导致错误的共同模式
        * 共享服务：该应用设计为在父组件和子组件之间共享一个服务。子组件为服务设置一个值，继而通过更新父组件的属性实现反应，我称这个父属性的更新为间接的，因为与上面的例子不同，现在子组件更新父组件属性不是非常显著的。
        * 同步事件广播（Synchronous event broadcasting）：该应用设计为一个子组件发送一个事件，然后父组件监听这个事件，该事件会导致父组件一些属性值的更新。同时这些属性被用于子组件的输入绑定中。这也是一个间接的父组件属性更新。
        * 动态组件实例化（Dynamic component instantiation）ngAfterViewInit：这种模式不同于之前输入绑定受到影响，而是会引起dom更新操作抛出错误。该应用设计为在父组件的ngAfterViewInit中动态的添加一个子组件，由于添加子组件需要dom修改，dom更新后继而触发ngAfterViewInit生命周期钩子，抛出错误。
    * 可能的修复解决方案
        * 异步属性更新：setTimeout(() => {this.parent.text = 'updated text';});
        * 强制更新检测：没有报错，似乎正确工作了，但是该解决方案存在一个问题，当触发对父组件的更新检测时，angular将运行对所有子组件的变更检测，会存在父组件属性更新的可能。

```
export class AppComponent {
    name = 'I am A component';
    text = 'A message for the child component';
    constructor(private cd: ChangeDetectorRef) {
    }
    ngAfterViewInit() {
        this.cd.detectChanges();
    }
}
```