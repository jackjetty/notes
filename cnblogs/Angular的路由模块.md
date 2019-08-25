* 路由配置可以设置在app-routing.module.ts中
* 用的是@angular/router
* 可以使用this.router.navigate(['/dashboard', this.asset.assetId])或者this.router.navigateByUrl进行跳转，注意这里的虚拟目标路径都要在后面的路由配置文件中定义好，包括跳转到外部地址
* 定义一个路由配置文件

```
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { OverviewPage } from './modules/overview/pages/overview.page';

const routes: Routes = [
  { path: 'dashboard/:id', loadChildren: './modules/dashboard/dashboard.module#DashboardModule' },
  { path: 'alarms/:id', loadChildren: './modules/alarms/alarms.module#AlarmsModule' },
  { path: 'spindlemonitor/:id', loadChildren: './modules/spindle-monitor/spindle-monitor.module#SpindleMonitorModule' },
  { path: 'aspects/:id', loadChildren: './modules/aspects/aspects.module#AspectsModule' },
  { path: 'map/:id', loadChildren: './modules/multimap/multimap.module#MultimapModule' },
  { path: 'overview', component: OverviewPage },
  { path: '', redirectTo: '/overview', pathMatch: 'full' },
  { path: '**', redirectTo: '/overview' }
];
@NgModule({
  imports: [
    RouterModule.forRoot(
      routes
    )
  ],
  exports: [
    RouterModule
  ]
})
export class AppRoutingModule { }
```

* 注意
    * 如果a标签等上的跳转连接写的是#锚点，那么虽然是页面内的定位跳转，貌似也会走路由设置，所以要小心子路径下的页面中的锚点连接可能会由于匹配不到前面的路由而走到最后的默认路由设置，而跳转到根页面或者默认页面。