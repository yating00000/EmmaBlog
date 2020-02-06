---
date: 2018-12-07 15:30:00
title: 表單驗證1
tags: [
  "angular",
  "實作心得"
]
categories: [
  "angular",
]
keywords:
  [
    "angular",
    "angular form",
    "angular validator",
    "angular validate",
    "angular FormControl",
    "angular formGroup",
    "angular formControlName"
  ]
comment: true
---

- 驗證模式的幾種方式
- 顯示錯誤的幾種寫法
  <!--more-->

# 驗證模式的幾種方式

## [(ngModel)]

- 在post.component.html

```js
    <input type="text" placeholder="請輸入帳號" 
    [(ngModel)]="account" name="account" 
    #aa="ngModel" minlength="6" 
    pattern="^([a-zA-Z]+\d+|\d+[a-zA-Z]+)[a-zA-Z0-9]*$" required>
```

 `[(ngModel)]="account"`只是雙向綁定資料,須加上`#aa="ngModel"`來控制


> 寫法不一定要有`<form>`標籤,

> 但如果有的話,使用[(ngModel)]的input要加上`name="account"`,否則會報錯.
` Error: If ngModel is used within a form tag, either the name attribute must be set or the form
control must be defined as 'standalone' in ngModelOptions.`

## FormBuilder

- 在component裡

```js
export class MasterInsertComponent implements OnInit {
  form: FormGroup;

  constructor(
    private fb: FormBuilder
  ) {}

  ngOnInit() {
    this.createForm();
  }

  createForm() {
    let obj = {
      account: ["",
        [
          Validators.required,
          ValidationService.userValidator,
          Validators.minLength(6)
        ]
      ]
    };
    this.form = this.fb.group(obj);
  }
}
```

- 在html裡

```html
<form [formGroup]="myForm">
  <input type="text" formControlName="account"/></div>
</form>
```

## FormControl

算是FormBuilder的零件版

- post.component.ts

```js
ipContent = new FormControl("", [
  Validators.required,
  ValidationService.ipValidator
]);
```

- post.component.html

可不使用`<form>`標籤

```html
<input type="text" [formControl]="ipContent">
<button (click)="enter()" [disabled]="!ipContent.valid">insert</button>
```

> 跟`FormBuilder`驗證方式一樣


# 顯示錯誤的寫法
主要是判斷目標的 errors 物件

## 寫在 html 上

```html
<input type="text" placeholder="請輸入帳號" 
[(ngModel)]="account" name="account" #aa="ngModel" 
minlength="6" pattern="^([a-zA-Z]+\d+|\d+[a-zA-Z]+)[a-zA-Z0-9]*$" required>

<span *ngIf="aa.touched && aa.errors && aa.errors.minlength">至少6位數</span>
<span *ngIf="aa.touched && aa.errors && aa.errors.required">必填</span>
<span *ngIf="aa.touched && aa.errors && aa.errors.pattern">格式錯誤</span>
```


常用的就

- aa.touched

- aa.invalid

- 以及aa.errors.*

	如:
	- aa.errors.minlength
	- aa.errors.pattern
  - aa.errors.required


## 寫在 component 上

- 在post.component.html

```html
<input type="text" placeholder="請輸入帳號" 
[(ngModel)]="account" name="account" #aa="ngModel" minlength="6" required>
<span *ngIf="aa.touched && aa.invalid" class="tip">
    {{errorMessage(aa)}}
</span>
```

- 在post.component.ts

```js
errorMessage(control) {
    if (!!control.errors) {
        if (!!control.errors.required) {
            return "必填"
        }
        if (!!control.errors.minlength) {
            return "至少6位數"
        }
        if (!!control.errors.pattern) {
            return "格式錯誤"
        }
    }
    return null;
}
```

## 統一放在子組件上

```js
import { Component, Input } from "@angular/core";
import { FormControl } from "@angular/forms";
@Component({
  selector: "validation-messages",
  template:
    '<div *ngIf="errorMessage !== null">{{errorMessage}}</div>',
  styles: [".error-message{color:#DF7607;font-size: x-small;}"]
})
export class ValidationComponent {
  @Input() control: FormControl;
  constructor() {}
  get errorMessage() {
    if (!!this.control) {
      for (let propertyName in this.control.errors) {
        if (
          this.control.errors.hasOwnProperty(propertyName) &&
          this.control.touched
        ) {
          return propertyName;
        }
      }
    }
    return null;
  }
}
```

- 在需要使用的地方(html)寫上

```html
<validation-messages [control]="aa"></validation-messages>
```
