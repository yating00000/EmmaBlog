---
date: 2020-06-20T11:37:54+08:00
title: "datetimepicker"
tags: ["angular", "實作心得"]
categories: ["angular"]
keywords:
  [
    "angular",
    "angular datetimepicker",
    "angular timepicker",
    "angular datepicker",
    "owl-date-time",
    "angular-material-components",
  ]
comment: true
---

- 套件選擇與說明
- 初始建置
- 一般用法
<!--more-->

# 套件選擇與說明

因 Angular Materia 的時間套件只能年月日，

但是工作上需要使用到時分秒，

早期使用都是用 `owl-date-time` 這個套件：

https://github.com/DanielYKPan/date-time-picker



那相關的使用方式，可參閱我在鐵人賽所寫的：

https://ithelp.ithome.com.tw/articles/10226541


---

然而現在因考慮到更新套件的作者有無`定期更新`!!

而且 angular 官方又是一直在持續更新版本，

所以考慮再三之下，

轉移到目前一直有在更新套件的 `angular-material-components`，

https://github.com/h2qutc/angular-material-components



那這套本身除了 datetimepicker，

作者還有做 colorpicker 等等一些小插件，弄成一包供大家使用。


> 本文只專注在`datetimepicker`上


# 初始建置

因為很多 `component` 都會用到 datetimepicker，

因此我會先把他包成一塊`Module` 方便共用。


```ts
import { NgModule } from "@angular/core";
import {
  NgxMatDatetimePickerModule,
  NgxMatTimepickerModule,
} from "@angular-material-components/datetime-picker";
import { NgxMatMomentModule } from "@angular-material-components/moment-adapter";
import { MatDatepickerModule, MAT_DATE_LOCALE, DateAdapter } from "@angular/material";
import { MomentDateAdapter, MatMomentDateModule } from "@angular/material-moment-adapter";

@NgModule({
  providers: [
    {
      provide: DateAdapter,
      useClass: MomentDateAdapter,
      deps: [MAT_DATE_LOCALE],
    },
    { provide: MAT_DATE_LOCALE, useValue: "zh-TW" },
  ],
  imports: [
    MatDatepickerModule,
    MatMomentDateModule,
    NgxMatDatetimePickerModule,
    NgxMatTimepickerModule,
    NgxMatMomentModule,
  ],
  exports: [
    MatDatepickerModule,
    MatMomentDateModule,
    NgxMatDatetimePickerModule,
    NgxMatTimepickerModule,
    NgxMatMomentModule,
  ],
})
export class MateriaDateModule {}
```

- package.json

建議要有以下安裝：

```
"@angular-material-components/datetime-picker": "^2.0.4",
"@angular-material-components/moment-adapter": "^2.0.1",
"@angular/material-moment-adapter": "^9.0.0",
"moment": "^2.24.0",
```

> 我習慣用 moment 型態，比較少使用 Date 型態

# 一般用法

```ts
import * as moment from "moment";

@Component({
  selector: "dialog-condition",
  templateUrl: "./dialog-condition.component.html",
  styleUrls: ["./dialog-condition.component.css"],
})
export class DialogConditionComponent implements OnInit {
  nowMoment = moment().format(); //現在時間
}
```

---

## 紀錄一下在表單中使用：

- component

```ts
...
import * as moment from "moment";

@Component({
  selector: "app-search-date",
  templateUrl: "./search-date.component.html",
  styleUrls: ["./search-date.component.css"],
  changeDetection: ChangeDetectionStrategy.OnPush,
})

export class SearchDateComponent {
  @Output() sendEvent: EventEmitter<IControl> = new EventEmitter<IControl>();
  ...
  ss: FormControl = null;
  ee: FormControl = null;

  constructor(
    private changeDetectorRef: ChangeDetectorRef,
    private service: SearchService
  ) {}

  ngOnInit() {
    ...
  }

  ngOnDestroy() {
    ...
  }

  init() {
    this.ss = new FormControl(
      moment().format(),
      this.controlStartDate.fn
    );
    this.ee = new FormControl(
      moment().format(),
      this.controlEndDate.fn
    );
    ...
  }

  getControlStart(): IControl {
    let o = {
        value: moment(this.ss.value).format()
        valid: this.ss.valid
    }
    return o;
  }

  getControlEnd(): IControl {
    let o = {
        value: moment(this.ee.value).format()
        valid: this.ee.valid
    }
    return o;
  }

  getSearch() {
    this.sendEvent.emit(this.getControlStart());
    this.sendEvent.emit(this.getControlEnd());
  }
}
```

要取得資料時：

> `moment(this.ee.value).format();`

---

- html

```html
<div *ngIf="!!controlStartDate && !!ss" (click)="pickerStart.open()">
  <input
    [ngxMatDatetimePicker]="pickerStart"
    [formControl]="ss"
    [max]="ee.value"
    (dateChange)="getSearch()"
  />
  <ngx-mat-datetime-picker #pickerStart [touchUi]="true"></ngx-mat-datetime-picker>
</div>
<validation-messages [control]="ss"></validation-messages>

<div *ngIf="!!controlEndDate && !!ee" (click)="pickerEnd.open()">
  <input
    [ngxMatDatetimePicker]="pickerEnd"
    [formControl]="ee"
    [min]="ss.value"
    (dateChange)="getSearch()"
  />
  <ngx-mat-datetime-picker #pickerEnd [touchUi]="true"></ngx-mat-datetime-picker>
</div>
<validation-messages [control]="ee"></validation-messages>
```

## 其他狀況

如果專案裡面所要的時間選擇器，
除了需要時分秒，也同時存在不要時分秒的時候，
這時候就採用 `Angular Materia` 原生的 datepicker，
套件可以完全相容。

- component

```ts
import * as moment from "moment";

@Component({
  selector: "app-customer-detail",
  templateUrl: "./detail.component.html",
  styleUrls: ["./detail.component.css"],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class CustomerDetailComponent implements OnInit, OnChanges {
  form: FormGroup;

  constructor(private fb: FormBuilder) {}

  ...

  insertForm() {
    let obj = {
      ...
      birthday: [moment().format(), Validators.required],
    };
    ...
    this.form = this.fb.group(obj);
  }

  editForm() {
    ...
    this.form.patchValue({
      ...
      birthday: this.select.birthday || moment().format(),
    });
  }

  getData(): ICustomer {
    let obj = <ICustomer>{
      ...
      birthday: moment(this.form.value.birthday).format("YYYY-MM-DD"),
    };
    ...
    return obj;
  }
}
```

---

- html

```html
<div class="item-row set-ipt" (click)="datepicker.open()">
  <input
    formControlName="birthday"
    [placeholder]="'import_birthday' | translate"
    [matDatepicker]="datepicker"
  />
  <mat-datepicker #datepicker></mat-datepicker>
</div>
<validation-messages [control]="form.controls.birthday"></validation-messages>
```
