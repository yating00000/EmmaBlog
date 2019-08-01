---
title: 表單驗證2-ngModel
categories:
  - angular
tags:
  - angular
  - 實作心得
keywords:
  - angular
  - angular6
  - form
  - validator
  - directive
abbrlink: b98fb95d
date: 2018-12-10 15:00:00
description:
---

- 簡單的 html 驗證
- 用 directive 驗證
- 常見情境-確認密碼 Confirm Password
- 備註:模板變數(#name) ngIf 與 hidden
  <!--more-->

因實作過程中,很多功能模塊都會用到,
因此顯示錯誤的 function 都會統一放在子組件,

```js
import { Component, Input } from "@angular/core";
import { FormControl } from "@angular/forms";
@Component({
  selector: "validation-messages",
  template:
    '<div class="error-message" *ngIf="errorMessage !== null">{{errorMessage}}</div>',
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

---

# 簡單的 html 驗證

例如:在 post.component.html

```

<div class="item-list">
  <span>account</span>
  <input type="text" placeholder="請輸入帳號" [(ngModel)]="account"
  name="account" #aa="ngModel" minlength="6"
  pattern="^([a-zA-Z]+\d+|\d+[a-zA-Z]+)[a-zA-Z0-9]*$" required>
  <validation-messages [control]="aa"></validation-messages>
</div>
<button (click)="send()" [disabled]="aa.invalid">
  submit
</button>
    
```

- 要把 input 變成控件,能夠使用 invalid,valid,touch,touched...等,就是加上模板變數 `#aa="ngModel"`
- `<form>`標籤可加可不加

以下例子是有加的

```
<div>
  <form #myForm="ngForm">
    <div class="item-list">
      <span>account</span>
      <input type="text" placeholder="請輸入帳號"
       [(ngModel)]="account"
      name="account" #aa="ngModel" minlength="6"
      pattern="^([a-zA-Z]+\d+|\d+[a-zA-Z]+)[a-zA-Z0-9]*$" required>
      <validation-messages [control]="aa"></validation-messages>
    </div>
    <button (click)="submit(myForm)" [disabled]="myForm.form.invalid">submit</button>
  </form>
</div>
```
- `<form>`標籤也加上模板變數後,在`<button>`的 disable 就能驗證整個表單內的元素是否都有效

---

# 用 directive 驗證

我們可能有很多需要自製的驗證方式,
所以除了原本 html 帶來的驗證方法,
我們可以使用 directive 來幫目標`input`綁定錯誤事件

## 一般寫法

在 fn-validator.directive.ts

```js
@Directive({
  selector: "[fnName]",
  providers: [
    {
      provide: NG_VALIDATORS,
      useExisting: forwardRef(() => fnValidator),
      multi: true
    }
  ]
})
export class fnValidator implements Validator {
  @Input() fnName: string;

  validate(c: AbstractControl): { [key: string]: any } {
    return fnValidator[this.fnName](c);
  }

  static passwordValidator(control: FormControl) {
    if (
      !!control.value &&
      !!control.value.match &&
      control.value.match(/^(?=.*[0-9])[a-zA-Z0-9!@#$%^&*]{6,100}$/)
    ) {
      return null;
    } else {
      return { invalid_password: true };
    }
  }

  static userValidator(control: FormControl) {
    //英數字+@
    if (
      !!control.value &&
      !!control.value.match &&
      control.value.match(/^.[A-Za-z0-9@]+$/)
    ) {
      return null;
    } else {
      return { invalid_account: true };
    }
  }
}
```

在要使用的 html 裡

```
<form #myForm="ngForm">
  <div class="item-list">
    <span>account</span>
      <input type="text" placeholder="請輸入帳號"
      [(ngModel)]="account" name="account" #aa="ngModel"
      [fnName]="'userValidator'">
      <validation-messages [control]="aa"></validation-messages>
    </div>
    <div class="item-list">
      <span>password</span>
      <input type="text" placeholder="請輸入密碼"
      [(ngModel)]="password" name="password" #pp="ngModel"
      [fnName]="'passwordValidator'">
      <validation-messages [control]="pp"></validation-messages>
    </div>
    <button (click)="submit(myForm)"
    [disabled]="myForm.form.invalid">submit</button>
  </form>
```

---

## 把驗證方法另外封裝

其實驗證的模式很多種,
我們會把經常用到的方法封裝到 service,
讓其他模式下也能用

在 fn-validator.directive.ts

```js
import {
  Input,
  OnInit,
  Directive,
  forwardRef,
  provide,
  Attribute
} from "@angular/core";
import { Validator, AbstractControl, NG_VALIDATORS } from "@angular/forms";
import { ValidationService } from "../share/validation.service";

@Directive({
  selector: "[fnName]",
  providers: [
    {
      provide: NG_VALIDATORS,
      useExisting: forwardRef(() => fnValidator),
      multi: true
    }
  ]
})
export class fnValidator implements Validator {
  @Input() fnName: string;

  validate(c: AbstractControl): { [key: string]: any } {
    return ValidationService[this.fnName](c);
  }
}
```

在 validation.service.ts

```js
import { FormControl } from "@angular/forms";

export class ValidationService {
  static passwordValidator(control: FormControl) {
    if (
      !!control.value &&
      !!control.value.match &&
      control.value.match(/^(?=.*[0-9])[a-zA-Z0-9!@#$%^&*]{6,100}$/)
    ) {
      return null;
    } else {
      return { invalid_password: true };
    }
  }

  static userValidator(control: FormControl) {
    //英數字+@
    if (
      !!control.value &&
      !!control.value.match &&
      control.value.match(/^.[A-Za-z0-9@]+$/)
    ) {
      return null;
    } else {
      return { invalid_account: true };
    }
  }
}
```

在要使用的 html 裡

```
<form #myForm="ngForm">
  <div class="item-list">
    <span>account</span>
      <input type="text" placeholder="請輸入帳號"
      [(ngModel)]="account" name="account" #aa="ngModel"
      [fnName]="'userValidator'">
      <validation-messages [control]="aa"></validation-messages>
    </div>
    <div class="item-list">
      <span>password</span>
      <input type="text" placeholder="請輸入密碼"
      [(ngModel)]="password" name="password" #pp="ngModel"
      [fnName]="'passwordValidator'">
      <validation-messages [control]="pp"></validation-messages>
    </div>
    <button (click)="submit(myForm)"
    [disabled]="myForm.form.invalid">submit</button>
  </form>
```
---

# 常見情境-確認密碼 Confirm Password

因為需要一個被比較是否相等的控件,
所以會另外做指令

在 equal-validator.directive.ts
```js
@Directive({
  selector: '[validateEqual][formControlName],[validateEqual][formControl],[validateEqual][ngModel]',
  providers: [
    { provide: NG_VALIDATORS, useExisting: forwardRef(() => EqualValidator), multi: true }
  ]
})
export class EqualValidator implements Validator {
  constructor(@Attribute('validateEqual') public validateEqual: string) {
  }

  validate(c: AbstractControl): { [key: string]: any } {
    let v = c.value;
    let e = c.root.get(this.validateEqual);
    if (e && v !== e.value)
      return { validateEqual: false }
    return null;
  }
}
```
在要使用的 html 裡

```
<form #myForm="ngForm">
  <div class="item-list">
    <span>account</span>
      <input type="text" placeholder="請輸入帳號" [(ngModel)]="account" name="account" #aa="ngModel" 
      [fnName]="'userValidator'">
      <validation-messages [control]="aa"></validation-messages>
    </div>
    <div class="item-list">
      <span>password</span>
      <input type="text" placeholder="請輸入密碼" [(ngModel)]="password" name="password" #pp="ngModel"
      [fnName]="'passwordValidator'">
      <validation-messages [control]="pp"></validation-messages>
    </div>
    <div class="item-list">
      <span>repeat</span>
      <input type="text" placeholder="請重複密碼" [(ngModel)]="repeat" name="repeat" #rr="ngModel" validateEqual="password">
    </div>
    <validation-messages [control]="rr"></validation-messages>
    <button (click)="submit(myForm)" [disabled]="myForm.form.invalid">submit</button>
  </form>
```
---

# 模板變數(#name) *ngIf 與 [hidden]

網上可以找到很多`*ngIf`跟`[hidden]`之間的差異,

簡單說就是`*ngIf`是將html元素從Dom加入或移除,

而`[hidden]`它會在Dom裡並沒有被破壞只是看不見而已.

曾在開發的時候,同個Component因為資料的不同,
導致需要顯示的input也不盡相同

```
<div *ngIf="!topData">
 <input type="text" #account="ngModel"
    [(ngModel)]="AgentAccountSel"
  />
  <validation-messages [control]="account"></validation-messages>
</div>
....
....
<button
  *ngIf="!topData"
  (click)="searchList()"
  class="search"
  [disabled]="account.invalid"
  [ngClass]="{ disabled: account.invalid}"
>
</button>
```

此時會出現錯誤,它抓不到`account`,沒有辦法判斷`account.invalid`,

上述`<button>`跟`<div>`他們的`*ngIf`條件一樣,

但因為`<div *ngIf="!topData">`有可能在Dom上找不到,

因此要採用`[hidden]`的方式

```
<div [hidden]="!!topData">
 <input type="text" #account="ngModel"
    [(ngModel)]="AgentAccountSel"
  />
  <validation-messages [control]="account"></validation-messages>
</div>
....
....
<button
  *ngIf="!topData"
  (click)="searchList()"
  class="search"
  [disabled]="account.invalid"
  [ngClass]="{ disabled: account.invalid}"
>
</button>
```
