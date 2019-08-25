* [官网](https://www.npmjs.com/package/ngx-infinite-scroll)
    * 注意版本要和Angular的版本对应
* 使用
    * package.json中增加依赖："ngx-infinite-scroll": "0.8.3",
    * module中导入infinite scroll模块：

```
import { InfiniteScrollModule } from 'ngx-infinite-scroll';

@NgModule({
  imports: [InfiniteScrollModule]
})
```

    * html中要使用infinite scroll的div修改成：
        * 也可以像官网那样，通过template在ts中填充div的html
        * **div要有合适的明确的height、overflow属性，让div有合适的高度，并且可以滚动。同时要设置[scrollWindow]="false"才能自定义滚动时的callback**
        * div上标识infiniteScroll
            * 说明这个div要使用infinite scroll功能
        * div上的[infiniteScrollDistance]="2"
            * the bottom percentage point of the scroll nob relatively to the infinite-scroll container (i.e, 2 (2 * 10 = 20%) is event is triggered when 80% (100% - 20%) has been scrolled). if container.height is 900px, when the container is scrolled to or past the 720px, it will fire the scrolled event.
        * div上的[infiniteScrollThrottle]="50"
            * should get a number of milliseconds for throttle. The event will be triggered this many milliseconds after the user stops scrolling.
        * div上的[scrollWindow]="false"
            * listens to the window scroll instead of the actual element scroll. this allows to invoke a callback function in the scope of the element while listenning to the window scroll.
        * div上的(scrolled)="onScroll()"
            * 设置了滚动时触发的脚本，主要用来更新view对应的model数据
            * this will callback if the distance threshold has been reached on a scroll down.

```
<div id="scroll-div" class="row scroll-div" *ngIf="dataForCurrentPage; else assetoeeloading" infiniteScroll
  [infiniteScrollDistance]="2" [infiniteScrollThrottle]="50" (scrolled)="onScroll()" [scrollWindow]="false">
  <div class="col-12 col-sm-12 col-md-6 col-lg-3 col-xl-2 pb-3" *ngFor="let data of dataForCurrentPage">
    <amp-oee-tile [data]="data" [timeframe]="currentTimeFrame" [kpi]="selectedKpi"></amp-oee-tile>
  </div>
</div>
```

    * 在ts文件中定义OnScroll函数
        * 主要用来更新view对应的model数据
        * 根据实际情况，可能要再次分页做数据请求；也可能第一次时已拿到所有的数据，只需要再追加一页的数据即可。

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs/Subscription';
import { AssetOee } from '../models/asset-oee.model';
import { TimeframeService } from '../shared/timeframe.service';
import { IAssetL } from '../../shared/asset-list/asset.model';
import { NavPageContext } from '../../shared/navbar/nav-page.component';
import { OeeService } from '../shared/oee.service';

const modalsContext: NavPageContext = {
  title: 'NAVBAR.PERFORMANCE_MONITORING.label',
  pageName: 'performance-monitoring',
  customUrl: 'performance-monitoring'
};

@Component({
  selector: 'amp-oee-overview',
  templateUrl: './oee-overview.component.html',
  styleUrls: ['./oee-overview.component.scss']
})
export class OeeOverviewComponent implements OnInit, OnDestroy {

  allData: AssetOee[];
  currentData: AssetOee[];
  currentTimeFrame: object;
  selectedKpi: string;
  selectedAsset: IAssetL;
  timeframeServiceSubscription: Subscription;
  private pageIndex = 0;
  private readonly pageSize = 10;
  private readonly defaultDataCount = 20;

  constructor(
    private readonly oeeService: OeeService,
    private readonly timeframeService: TimeframeService) {
  }

  onAssetSelected(asset: IAssetL) {
    this.selectedAsset = asset;
    if (this.currentTimeFrame) {
      this.getAssets(this.selectedAsset.id);
    }
  }

  /**
   * ngOnInit()
   * This function gets called during the creation-process
   * of this component.
   */
  ngOnInit() {
    this.timeframeServiceSubscription = this.timeframeService.getSelectedtimeframe().subscribe(
      currentTimeFrame => {  // subscribe to the timeframeservice to get notified on changes
        this.currentTimeFrame = currentTimeFrame;
        if (window.location.pathname.substring(1) === modalsContext.pageName) {
          if (this.selectedAsset) {
            this.getAssets(this.selectedAsset.id); // get the KPIs and trends of the current asset
          }
        }
      });
  }

  ngOnDestroy() {
    this.timeframeServiceSubscription.unsubscribe();
  }

  KPIchanged(kpi?: string) {
    this.selectedKpi = kpi;
  }

  sortByTypeAndName(a, b): number {
    if (a.hasChildren === b.hasChildren) {
      const assetnameA = a.assetName.toLowerCase();
      const assetnameB = b.assetName.toLowerCase();
      if (assetnameA > assetnameB) {
        return 1;
      }
      if (assetnameA < assetnameB) {
        return -1;
      }
      return 0;
    } else if (a.hasChildren === true) {
      return -1;
    } else {
      return 1;
    }
  }

  /**
   * getAssets()
   * This function calls the mindSphereService to get the
   * list of child assets and their OEE-value for the
   * given timeframe
   * @param assetId
   */
  getAssets(assetId?: string): void {
    const period = this.currentTimeFrame['value'];

    if (assetId === undefined) { // check if an assetId has been provided
      assetId = this.selectedAsset.id;
    }

    if (this.selectedAsset.hasSubElements) {
      this.oeeService.getChildrenOee(assetId, period) // get the list of all child assets from the mindsphereservice
        .subscribe(data => {
          data.forEach(asset => {
            if (asset['hasChildren'] === true) {
              asset['type'] = 'factory';
            } else {
              asset['type'] = 'machine';
            }
          });
          data.sort(this.sortByTypeAndName);
          this.allData = data as AssetOee[];
          this.loadData();
        });
    } else {
      this.oeeService.getAssetOee(assetId, period) // get the oee data of that asset from the mindsphereservice
        .subscribe(asset => {
          asset['type'] = 'machine';
          this.allData = asset as AssetOee[];
          this.loadData();
        });
    }
  }

  // When scroll down the screen
  onScroll() {
    this.pageIndex = this.pageIndex + 1;
    this.loadData();
  }

  loadData() {
    if (this.allData) {
      this.currentData = this.allData.slice(0, this.defaultDataCount + this.pageIndex * this.pageSize);
    }
  }

}
```