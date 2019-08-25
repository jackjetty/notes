* 核心是Observable<any>对象
* of(object)可以将一个对象转换成Observable对象
* pipe
    * 单独使用
        * this.http.get<any>(this.iotTsAggregatesApiUrl + this.aggregatesEndpoint + id + '/SpindleMonitorConfig', { params }).pipe(catchError(err => this.handleHttpError(err)));
    * 结合map、mergeMap、expand进行数据处理
        * Observable<any>.pipe(map((data: any) => {}));
        * Observable<any>.pipe(mergeMap((data: any) => {}));
        * Observable<IAssetInfo | MmmError>.pipe(expand( (result: IAssetInfo) => {}), map(obj => obj)).subscribe((val) => {});
* subscribe