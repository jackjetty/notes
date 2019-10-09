* ngIf
    * 控制元素显示与否、显示哪一个元素。用#定义标识。ngIf直接判断变量是否不为空，不为空即显示自己，否则显示else后面用#指定的元素。
    * <div*ngIf="data; else loading"></div>
    * <ng-template #loading></ng-template>
* ngFor
    * 其中可以使用async管道，如*ngFor="let asset of assetSummaryList | async;let index=index;"
        * [https://www.concretepage.com/angular-2/angular-2-async-pipe-example](https://www.concretepage.com/angular-2/angular-2-async-pipe-example)
* ngClass
    * 可以控制多个class
    * 可以通过变量来动态控制本元素是否使用某个class，如<xxx-content id="yyy-content" [ngClass]="{'clear-margin-bottom':isClearContentMarginBottom, 'clear-margin-bottom2':isClearContentMarginBottom2}"></xxx-content>，其中clear-margin-bottom和clear-margin-bottom2是样式class的名字，isClearContentMarginBottom和isClearContentMarginBottom2是boolean类型的变量。