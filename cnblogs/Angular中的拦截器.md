* 拦截器是按顺序的？拦截器链？
    * 在app.module.ts中，可以设置APP使用的拦截器

```
@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    FormsModule,
    CoreModule,
    SharedModule,
    OverviewModule,
    HttpClientModule,
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useFactory: (createTranslateLoader),
        deps: [HttpClient]
      },
      isolate: false
    }),
    AppRoutingModule
  ],
  providers: [
    { provide: ErrorHandler, useClass: GlobalErrorHandler },
    { provide: HTTP_INTERCEPTORS, useClass: ServerErrorInterceptor, multi: true }
  ],
  bootstrap: [AppComponent],
  schemas: [
    CUSTOM_ELEMENTS_SCHEMA
  ]
})
```

* 错误拦截器
    * 和java等很类似，关键是实现了ErrorHandler接口，然后实现了handleError(error: Error | HttpErrorResponse)方法。
        * error是封装的HttpErrorResponse等对象，其中包含一些message等信息

```
import { ErrorHandler, Injectable, Injector } from '@angular/core';
import { HttpErrorResponse } from '@angular/common/http';
import { ErrorService } from './error.service';
import { LoggerService } from '../logging/logger.service';
import { NotificationService } from '../notification/notification.service';


@Injectable()
export class GlobalErrorHandler implements ErrorHandler {

  constructor(private injector: Injector) { }

  handleError(error: Error | HttpErrorResponse) {

    const errorService = this.injector.get(ErrorService);
    const logger = this.injector.get(LoggerService);
    const notifier = this.injector.get(NotificationService);

    let message;
    let stackTrace;
    if (error instanceof HttpErrorResponse) {
      // Server error
      message = errorService.getServerErrorMessage(error);
      //stackTrace = errorService.getServerErrorStackTrace(error);
      notifier.showError(message);
    } else {
      // Client Error
      message = errorService.getClientErrorMessage(error);
      notifier.showError(message);
    }
    // Always log errors
    logger.error(message, stackTrace);
  }
}
```

* http拦截器
    * 和java等很类似，关键是实现了HttpInterceptor接口，然后实现了intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>方法。
        * next.handle(req)是发送请求，后面.pipe(mergeMap((event: any) => {xxxxxxxxxxxxx}),catchError((err: HttpErrorResponse) => this.handleData(err)));是通过管道和转换进行业务处理
        * event是封装的HttpResponse或HttpRequest对象，其中包含一些url、status等信息，类似于java中servlet中的框架给封装好的Response、Request对象。
    * request和response都可以拦截，下面的写法会按顺序先拦截request (status code是0?)，再拦截response。
        * 可以根据event instanceof HttpResponse来判断这一次进来的是request还是response
    * throwError会将Error抛给错误拦截器去处理，比如会给用户弹窗、记日志、输出到console
    * 可以使用局部变量作为判断变量，比如用于判断只在第一次符合条件时做某些事情
        * 如下面例子中的bDialogOpen
        * **虽然看起来是拦截器级别的变量，但是只要不刷新页面，好像就是存在的，也就是说作用于貌似是页面级的**

```
import { HttpErrorResponse, HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpResponse } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable, throwError, of } from 'rxjs';
import { catchError, retry, mergeMap } from 'rxjs/operators';
import { MatDialog } from '@angular/material';
import { MmmError } from '../models/mmm-error.model';
import { TimeoutDialogComponent } from 'app/modules/shared/components/timeout-dialog/timeout-dialog.component';
import { LoggerService } from '../logging/logger.service';
import { Router } from '@angular/router';
import { environment } from 'environments/environment';

@Injectable()
export class ServerErrorInterceptor implements HttpInterceptor {

    constructor(
        private router: Router,
        private dialog: MatDialog,
        private logger: LoggerService) { }

    bDialogOpen: boolean = false;
    loginurl: string = 'https://' + environment.tenant + '.piam.' + environment.domain + '/login';
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        return next.handle(req).pipe((
            mergeMap((event: any) => {
                if (event instanceof HttpResponse && event.status === 200 && event.url === this.loginurl && this.bDialogOpen === false) {
                    this.openSessionErrorDialog();
                }
                return of(event);
            }),
            catchError((err: HttpErrorResponse) => this.handleData(err))
        ).bind(this));
    }

    private handleData(
        event: HttpResponse<any> | HttpErrorResponse,
    ): Observable<any> {
        this.logger.debug('catch error code: ', event.status);
        switch (event.status) {
            case 401:
                break;
            default:
                break;
        }
        return throwError(event);
    }

    openSessionErrorDialog(): void {
        this.bDialogOpen = true;
        const dialogRef = this.dialog.open(TimeoutDialogComponent, {
          width: '550px',
          data: { reload: true }
        });

        dialogRef.afterClosed().subscribe((result => {
          // refresh the page
          this.bDialogOpen = false;
          if (result) {
            window.location.reload();
          }
        }).bind(this));
      }

}
```