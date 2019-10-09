* [官网](https://material.angular.io/)
* Data table
    * Table
    * Paginator
        * 和DataSource绑定时，特别要注意如果在ngIf中，那么用@ViewChild可能取到的控件会是undefined，这时候可以后面的方式来获取元素
    * Sort header
        * 和DataSource绑定时，特别要注意如果在ngIf中，那么用@ViewChild可能取到的控件会是undefined，这时候可以后面的方式来获取元素

```
  <table mat-table [dataSource]="dataSource" matSort>
    <ng-container matColumnDef="time">
      <th mat-header-cell *matHeaderCellDef mat-sort-header><span translate>ASPECTS.Table.timestamp</span></th>
      <td mat-cell *matCellDef="let row"> {{row.time  | date:'yyyy-MM-dd HH:mm:ss' : timezone}} </td>
    </ng-container>
    <ng-container matColumnDef="variable">
      <th mat-header-cell *matHeaderCellDef mat-sort-header><span translate>ASPECTS.Table.variable</span></th>
      <td mat-cell *matCellDef="let row"> {{row.variable}} </td>
    </ng-container>
    <ng-container matColumnDef="value">
      <th mat-header-cell *matHeaderCellDef mat-sort-header><span translate>ASPECTS.Table.value</span></th>
      <td mat-cell *matCellDef="let row"> {{row.value}} </td>
    </ng-container>
    <ng-container matColumnDef="qualityCode">
      <th mat-header-cell *matHeaderCellDef mat-sort-header><span translate>ASPECTS.Table.qualityCode</span></th>
      <td mat-cell *matCellDef="let row"> {{row.qualityCode}} </td>
    </ng-container>

    <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
    <tr mat-row *matRowDef="let row; columns: displayedColumns;">
    </tr>
  </table>

<mat-paginator [pageSizeOptions]="pageSizeOptions"></mat-paginator>




table {
  width: 100%;
  table-layout: fixed;
}

.title{
  font-weight: bold;
  width: 20%;
}
.value{
  width: 80%;
}
th, td {
  overflow: hidden;
  width:auto;
  text-overflow: ellipsis;
  white-space: nowrap;
}




export class TableComponent implements OnInit, OnChanges {
  @ViewChild(MatPaginator)
  set paginator(value: MatPaginator) {
    this.dataSource.paginator = value;
  }
  @ViewChild(MatSort)
  set sort(value: MatSort) {
    this.dataSource.sort = value;
  }
  @Input() variables;
  @Input() aspectName;
  @Input() selectedAssetId: string;
  @Input() dateRange;
  @Input() timezone: String = AspectsConstants.LOCAL_TIMEZONE;
  dataSource: MatTableDataSource<any>;
  displayedColumns: string[] = ['time', 'variable', 'value', 'qualityCode'];
  isEmpty: boolean = false;
  pageSizeOptions = [5, 10, 25, 100];
  loading: boolean= true;
  constructor(private iotTimeseriesService: IotTimeseriesService, public dateTimeService: DateTimeService) {
  }

  getData(): void {
    this.loading = true;
    this.iotTimeseriesService.getAspectData(
      this.selectedAssetId,
      this.aspectName,
      this.dateRange.from.toISOString(),
      this.dateRange.to.toISOString(),
      this.variables.join(),
      'desc'
    ).subscribe((aspectData) => {
      this.isEmpty = aspectData.length === 0;
      this.dataSource = new MatTableDataSource(this.map(aspectData));
      // this.dataSource.paginator = this.paginator;
      // this.dataSource.sort = this.sort;
      this.loading = false;
    });
  }

  map(aspectData: any): Array<TableData> {
    const table = new Array<TableData>();
    if (aspectData) {
      aspectData.forEach(aspectDataElement => {
        this.variables.forEach(variable => {
          const tableRow = new TableData();
          tableRow.time = aspectDataElement[AspectsConstants.TIMESTAMP];
          tableRow.variable = variable;
          tableRow.value = aspectDataElement[variable];
          tableRow.qualityCode = aspectDataElement[variable + AspectsConstants.QUALITY_CODE];
          table.push(tableRow);
        });
      });
    }
    return table;
  }

  ngOnChanges(changes: SimpleChanges): void {
    for (const change in changes) {
      if (!changes[change].isFirstChange()) {
        this.getData();
        return;
      }
    }
  }

  ngOnInit(): void {
    this.getData();
  }

}
```