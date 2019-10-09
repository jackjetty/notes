* 基本
    * 在项目根目录下执行ng test命令即执行单元测试
    * 每个component下面的**xxx.component.spec.ts**就是该component对应的单元测试文件
        * 使用ng generate xxx来生成component时会自动生成.spec.ts单元测试文件，也可以通过指定参数来不让它生成
    * 依赖
        * 如果该component依赖了新的module，即使是配在上级的xxx.module.ts中的，这个xxx.component.spec.ts单元测试文件中也要**单独引用该新module**。
            * 其实这个和java等语言的单元测试一样，单元测试本身不使用项目配置的依赖，而是自己进行引用。
        * 可以使用**@Component+class ScreenshotComponent**定义要依赖的component（其实和在单独的文件里定义component类似，只是class前面没有加export），用于后面引入依赖
            * 在这些定义的要依赖的component中，需要声明该业务component中的成员及类型，如selectedXXX;
        * 在这段代码中定义和编译依赖的module和component：describe('XXXComponent', () => {beforeEach(async(() => {TestBed.configureTestingModule({…...}).compileComponents();}));});
    * 测试准备
        * 这些包含在**beforeEach**中的脚本貌似只是相当于测试准备，类似于在Junit的BeforeTest中准备测试环境、资源。beforeEach可以有多个，可以有异步的。
    * jasmine.createSpyObj
* 下面的实例单元测试脚本，其实没有测什么逻辑，只是试着加载了一下component，看有没有报错。

```typescript
import { async, ComponentFixture, TestBed } from '@angular/core/testing';

import { XXXComponent} from './xxx.component';
import { Component, Input } from '@angular/core';
import { NavbarService } from '../../core/navbar/navbar.service';
import { of } from 'rxjs/observable/of';
import { ActivatedRoute } from '@angular/router';
import { Logger } from 'sinint-application-core-spa';
import { XXXService } from '../shared/xxx.service';
import { TimeframeService } from '../shared/timeframe.service';
import { XXX } from '../models/xxx.model';
import { InfiniteScrollModule } from 'ngx-infinite-scroll';

@Component({ selector: 'xxx-screenshot', template: '' })
class XXXScreenshotStubComponent {
  @Input() initWithRootAtStart = false;
  @Input() selectedXXX: string;
}

describe('XXXComponent', () => {
  let component: XXXComponent;
  let fixture: ComponentFixture<XXXComponent>;

  const urlSegment = [{ path: '', parameters: [] }];
  const route = { url: of(urlSegment), snapshot: '' };

  beforeEach(async(() => {
    const xxxService = jasmine.createSpyObj('XXXService', ['getChildrenXXX', 'getXXX']);
    const timeframeService = jasmine.createSpyObj('TimeframeService', ['getSelectedtimeframe']);
    const navbarService = jasmine.createSpyObj('NavbarService', ['canNavigateBack']);
    const logger = jasmine.createSpyObj('Logger', ['error', 'info']);

    timeframeService.getSelectedtimeframe.and.returnValue(of({ count: 24, unit: 'today', value: 'today' }));

    TestBed.configureTestingModule({
      imports: [InfiniteScrollModule],
      declarations: [
        XXXComponent
      ],
      providers: [
        { provide: XXXService, useValue: xxxService },
        { provide: TimeframeService, useValue: timeframeService },
        { provide: NavbarService, useValue: navbarService },
        { provide: ActivatedRoute, useValue: route },
        { provide: Logger, useValue: logger }
      ]
    })
      .compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(XXXComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

```