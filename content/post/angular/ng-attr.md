---
title: attr
categories:
  - angular
tags:
  - angular
  - 實作心得
keywords:
  - angular
  - angular6
  - angular attr
abbrlink: 4b1b4fc6
date: 2018-12-26 11:30:13
description:
---

- 在 angular 裡使用 `attr`
- 常見情境-響應式 Table
- 常見情境-直接在列表上更新
    <!--more-->

# 在 angular 裡使用 `attr`

一般我們都會做 html 的屬性綁定,如: `[attr.colspan]`
或是 value 不夠用時,要多增加一個自訂參數,
如: `[attr.change-val]` change-val 是我自訂的

---

# 常見情境-響應式 Table

目前的 RWD 的 table 做法請參見此網站

http://lab.25sprout.com/responsive_table/

那`attr`的好處可以使用`破壞 TABLE 排版`的方式

![](/img/ng_attr_2.gif)

也就是直接將 Thead 直接拿掉，

剩下的 tbody tr td 都使用 display:block 來排版

那在angular下可以這樣寫

```html
<table>
  <thead>
    <tr>
      <th>標題1</th>
      <th>標題2</th>
      <th>標題3</th>
      <th>標題4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td [attr.data-title]="'標題1'">A1</td>
      <td [attr.data-title]="'標題2'">A2</td>
      <td [attr.data-title]="'標題3'">A3</td>
      <td [attr.data-title]="'標題4'">A4</td>
    </tr>
    <tr>
      <td [attr.data-title]="'標題1'">B1</td>
      <td [attr.data-title]="'標題2'">B2</td>
      <td [attr.data-title]="'標題3'">B3</td>
      <td [attr.data-title]="'標題4'">B4</td>
    </tr>
  </tbody>
</table>
```

```js
//css
@media only screen and (max-width: 767px) {
  .table {
    display: block;
  }
  .table thead,
  .table tbody,
  .table tfoot,
  .table th,
  .table td,
  .table tr {
    display: block;
  }

  .table thead tr {
    position: absolute;
    top: -9999px;
    left: -9999px;
  }

  .table tbody tr td {
    height: auto;
    min-height: 30px;
    position: relative;
  }

  .table tbody tr td span,.table tbody tr td a {
    line-height: 34px;
  }

  .table td:before {
    position: absolute;
    top: 0;
    left: 10px;
    line-height: 40px;
  }

  .table td:before {
    content: attr(data-title);
  }

  tfoot tr td {
    position: relative;
  }
}
```
---

# 常見情境-直接在列表上更新
	
![](/img/ng_attr_1.gif)


在實務上有時候我們只需要改少數幾個欄位,
不見得會另做一頁表單式,

所以作法上就是在html元素上綁上自訂義的值,
在用監聽的方式,一但按下(click)後就開始做更新修改的事情

```js
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormControl, Validators } from "@angular/forms";
import { fromEvent as observableFromEvent } from "rxjs/internal/observable/fromEvent";
import { Observable } from "rxjs/internal/Observable";
import { Subscription } from "rxjs/internal/Subscription";

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  subscriptionClick: Subscription;
  fc: FormControl;
  a = "AAA"
  b = "BBB"
  c = "CCC"
  selectID = 0;
  constructor() { }

  ngOnInit() {
    this.subscriptionClick = observableFromEvent(window, "click").subscribe(
      (e: MouseEvent) => {
        if (!!e.srcElement.attributes.getNamedItem("change-val")) {
          let v = e.srcElement.attributes.getNamedItem("change-val");
          this.selectID = parseInt(v.nodeValue);
          this.fc = new FormControl("", [
            Validators.required,
          ]);
        }

        if (!e.srcElement.attributes.getNamedItem("change-val")) {
          if (!this.fc) {
            this.clickReset();
            return;
          }
          if (!!this.fc && this.fc.valid) {
            this.saveVal(this.fc.value)
            this.clickReset();
          }
        }
      }
    );
  }

  ngOnDestroy() {
    if (!!this.subscriptionClick) {
      this.subscriptionClick.unsubscribe();
    }
  }

  clickReset() {
    this.selectID = 0;
    if (!!this.fc) {
      this.fc.setValue("")
    }
  }

  saveVal(val: string) {
    switch (this.selectID) {
      case 1:
        this.a = val;
        break;
      case 2:
        this.b = val;
        break;
      case 3:
        this.c = val;
        break;
    }
  }
}
```

```
<div class="flex">
  <div>
    <span [attr.change-val]="1" *ngIf="selectID != 1">{{ a }}</span>
    <div *ngIf="selectID == 1 && !!fc">
      <input [attr.change-val]="1" [formControl]="fc" cusAutofocus /> 
      <button *ngIf="fc.touched && !fc.valid" (click)="clickReset()">X</button>
      <span *ngIf="fc.touched && fc.errors && fc.errors.required" class="tip">必填</span>
    </div>
  </div>
  <div>
    <span [attr.change-val]="2" *ngIf="selectID != 2">{{ b }}</span>
    <div *ngIf="selectID == 2 && !!fc">
      <input [attr.change-val]="2" [formControl]="fc" cusAutofocus /> 
      <button *ngIf="fc.touched && !fc.valid" (click)="clickReset()">X</button>
      <span *ngIf="fc.touched && fc.errors && fc.errors.required" class="tip">必填</span>
    </div>
  </div>
  <div>
    <span [attr.change-val]="3" *ngIf="selectID != 3">{{ c }}</span>
    <div *ngIf="selectID == 3 && !!fc">
      <input [attr.change-val]="3" [formControl]="fc" cusAutofocus /> 
      <button *ngIf="fc.touched && !fc.valid" (click)="clickReset()">X</button>
      <span *ngIf="fc.touched && fc.errors && fc.errors.required" class="tip">必填</span>
    </div>
  </div>
</div>
```

```
//css

.flex{
  display: flex;
}

.flex>*{
  border: 1px solid #333;
  margin:0 5px;
  padding: 0 5px;
}

button{
  margin-left:2px;
}

.tip{
  font-size: smaller;
  color:orange;
}
```