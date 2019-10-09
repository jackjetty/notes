# 1. 获取和操作DOM元素的样式等
* [Angular使用总结 --- 如何正确的操作DOM ](https://www.cnblogs.com/shapeY/p/9272961.html)
* [Angular4快速获取DOM元素，进行增删改样式等处理](https://blog.csdn.net/qq_28004379/article/details/80801378)
* 使用@ViewChild、@ViewChildren直接获取并操作元素
    * 比较好，因为不用先获取整个component的DOM，然后再在其中查找要操作的元素
    * @ViewChild
        * ViewChild 装饰器用于获取模板视图中的元素或直接调用其组件中的方法。它支持 Type 类型或 string 类型的选择器，同时支持设置 read 查询条件，以获取不同类型的实例。比如ElementRef和ViewContainerRef.
        * ViewChild 是属性装饰器，用来从模板视图中获取匹配的元素。视图查询在 ngAfterViewInit 钩子函数调用前完成，因此在ngAfterViewInit钩子函数中，才能正确获取查询的元素。
        * 使用
            * 先要在html文件中标识该元素
            * 然后在ts文件中声明和使用该元素
                * 通过其nativeElement成员进行后续DOM操作
                * 注意：由于该元素有可能不存在，比如是动态画出来的，所以最好在使用时先判断一下

```
    <button #refreshButton style="margin: 0 5px" class="btn" (click)="getKPIs()">
      <si-icon src="assets/icons/common/refresh.svg"></si-icon>
    </button>


import { Component, ViewChild, ElementRef, OnInit, OnDestroy, ViewEncapsulation } from '@angular/core';

export class OeeMachineDetailComponent extends NavPageComponent implements OnInit, OnDestroy {
  @ViewChild('refreshButton') refreshButton: ElementRef;

  this.refreshButton.nativeElement.blur();
}
```

    * @ViewChildren
        * ViewChildren 装饰器是用来从模板视图中获取匹配的多个元素，返回的结果是一个QueryList集合。
        * 使用
            * 先要在html文件中标识该元素
            * 然后在ts文件中声明和使用该元素
                * 框架帮忙注入的这个QueryList集合类型，本身就有changes等方法可用，可用于监听其变化。
                * 可以指定QueryList<any>的具体类型。如果是自己在html元素上加的#xxx，那么可以用any或者ElementRef，后者和@ViewChild得到的对象类型一样，可以通过其nativeElement成员继续操作该DOM元素；如果是自己定义或者引入的Component，那么可以直接是Component的类型名称。
                * 注意：由于该元素有可能不存在，比如是动态画出来的，所以最好在使用时先判断一下
                * 注意：**为什么当allAssetTiles元素是根据ngIf来控制是否存在时，用@ViewChildren可以直接操作它，而@ViewChild会报错说未定义？？？**
                    * 当在ngIf中而@ViewChild取不到该元素时，可以用后面的方式控制其初始化：

```
<div #scrollDiv id="scroll-div-showall-screenshot" class="row scroll-div" *ngIf="currentData; else assetoeeloading" infiniteScroll
  [infiniteScrollDistance]="2" [infiniteScrollThrottle]="50" (scrolled)="onScroll()" [scrollWindow]="false">
  <div #allAssetTiles class="col-12 col-sm-12 col-md-6 col-lg-4 col-xl-3 pb-3" *ngFor="let data of currentData;">
    <amp-oee-tile kpi="oee" [data]="data" [timeframe]="currentTimeFrame"></amp-oee-tile>
  </div>
</div>


import { Component, ElementRef, ViewChild, ViewChildren, QueryList, OnInit, OnDestroy, AfterViewInit } from '@angular/core';

export class OeeOverviewComponent implements OnInit, OnDestroy, AfterViewInit {
  @ViewChild('scrollDiv') scrollDiv: ElementRef;
  @ViewChildren('allAssetTiles') allAssetTiles: QueryList<ElementRef>;

  /**
   * ngAfterViewInit()
   * Scroll to previous position after all asset tiles refreshed
   * when switch timeframe selector or click refresh button.
   */
  ngAfterViewInit() {
    this.allAssetTiles.changes.subscribe(t => {
      // Scroll to previous saved position after before switch timeframe selector.
      if (this.scrollDiv) {
        this.scrollDiv.nativeElement.scrollTop = this.currentScrollTop;
      }
    });
  }
}
```

```
@ViewChild(MatPaginator)
  set paginator(value: MatPaginator) {
    this.dataSource.paginator = value;
  }
```

* 使用ElementRef
    * 麻烦点，需要先获取整个component的DOM，然后再在其中查找要操作的元素
    *  A wrapper around a native element inside of a View.
    * 在组件的 constructor中注入ElementRef，可以获取到整个组件元素的包裹。
    * 过程
        * 在ngOnInit()生命周期函数中通过this.el.nativeElement获取当前component的最外层DOM，并赋值给本地变量留待后用。
        * **注意如果想要操作的DOM元素时通过ngIf等指令控制的，那么有可能第一次运行到此处时还获取不到该DOM，但后面还会走到这里，因此最好在使用该DOM元素前先判断一下是否存在。**
        * 然后通过querySelector()函数来通过id、class来获取想要的更内层的DOM元素
            * **获得的其实是JavaScript或者JQuery对象，即后续可以使用JavaScript或者JQuery方法，如$(selector).blur()**

```
import { Component, ElementRef, ViewChildren, QueryList, OnInit, OnDestroy, AfterViewInit } from '@angular/core';

export class OeeOverviewComponent implements OnInit, OnDestroy, AfterViewInit {
  private scrollDiv: any;

  constructor(
    private el: ElementRef) {
  }

  ngOnInit() {
    this.scrollDiv = this.el.nativeElement;
  }

  /**
   * Save current scroll position before switch timeframe selector.
   */
  savePreviousScrollPosition() {
    if (this.scrollDiv && this.scrollDiv.querySelector('.scroll-div')) {
      this.currentScrollTop = this.scrollDiv.querySelector('.scroll-div').scrollTop;
      this.scrollPositionUpdated = true;
    }
  }

  /**
   * Scroll to previous saved position after before switch timeframe selector.
   */
  scrollToPreviousPosition() {
    if (this.scrollPositionUpdated && this.currentScrollTop && this.scrollDiv && this.scrollDiv.querySelector('.scroll-div')) {
      this.scrollDiv.querySelector('.scroll-div').scrollTop = this.currentScrollTop;
      this.scrollPositionUpdated = false;
    }
  }
}
```

* 使用Renderer2结合ElementRef
    * 在获取dom之后，如何对dom进行操作呢？原生的domAPI是一种选择，Angular提供了更好的跨平台方式   Renderer2

```
import { Component, ElementRef, Renderer2, Input, OnInit, OnDestroy, AfterViewInit, ViewChildren } from '@angular/core';

export class OeeOverviewComponent implements OnInit, OnDestroy, AfterViewInit {
  constructor(
    private el: ElementRef,
    private renderer2: Renderer2) {
  }

    this.render.setStyle(this.viewelement.nativeElement , 'color' , 'red');
}
```

* 使用@viewChild() 
* 使用@viewChildren

# 2. 监听DOM事件
* 使用ViewChildren
    * [官方文档](https://www.angular.cn/api/core/ViewChildren)
    * You can use ViewChildren to get the QueryList of elements or directives from the view DOM. Any time a child element is added, removed, or moved, the query list will be updated, and the changes observable of the query list will emit a new value.
    * View queries are set before the ngAfterViewInit callback is called.
    * **用于监听使用了ngFor等指令的循环div等元素时，subscribe的changes时触发在所有ngFor元素一个个都画完之后。因此也就可用于在ngFor指令所有循环结束后触发事件处理逻辑。**

```
<!--Tiles-->
<div id="scroll-div-showall-screenshot" class="row scroll-div" *ngIf="currentData; else assetoeeloading" infiniteScroll
  [infiniteScrollDistance]="2" [infiniteScrollThrottle]="50" (scrolled)="onScroll()" [scrollWindow]="false">
  <div #allAssetTiles class="col-12 col-sm-12 col-md-6 col-lg-4 col-xl-3 pb-3"
    *ngFor="let data of currentData; let last = last">
    <amp-oee-tile kpi="oee" [data]="data" [timeframe]="currentTimeFrame"></amp-oee-tile>
  </div>
</div>

import { Component, ElementRef, ViewChildren, QueryList, OnInit, OnDestroy, AfterViewInit } from '@angular/core';

export class OeeOverviewComponent implements OnInit, OnDestroy, AfterViewInit {
  @ViewChildren('allAssetTiles') allAssetTiles: QueryList<any>;

  ngAfterViewInit() {
    this.allAssetTiles.changes.subscribe(t => {
      this.scrollToPreviousPosition();
    });
  }
}
```

* 想要在ngFor加载完成最后一个元素后触发事件，还有一种方式，但有可能会有一点问题。
    * 自定义指令（如ready），然后根据ngFor中的index来判断是否处理到了最后一个元素，只有是最后一个的时候，才给自定义指令传入为true的参数，因而触发其中的逻辑。

```
<div id="scroll-div-showall-screenshot" class="row scroll-div" *ngIf="currentData; else assetoeeloading" infiniteScroll
  [infiniteScrollDistance]="2" [infiniteScrollThrottle]="50" (scrolled)="onScroll()" [scrollWindow]="false">
  <div class="col-12 col-sm-12 col-md-6 col-lg-4 col-xl-3 pb-3" *ngFor="let data of currentData; let last = last" [attr.ready]="last ? false : true">
    <amp-oee-tile kpi="oee" [data]="data" [timeframe]="currentTimeFrame"></amp-oee-tile>
  </div>
</div>

  @Input()
  set ready(isReady: boolean) {
    if (isReady) {
      // do something
    }
  }
```

* angular1中的方式？自定义指令？
    * 使用app.directive、$scope什么的
        * [angular指令：判断ng-repeat完成后的回调事件](https://blog.csdn.net/p493794782/article/details/72674688)