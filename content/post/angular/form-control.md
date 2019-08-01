---
title: 表單驗證4-FormControl
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
  - formbuilder
  - FormControl
  - formGroup
  - formControlName
abbrlink: 24c21d6f
date: 2018-12-12 15:15:57
description:
---

- 一般用法
- 有沒有`<form>`標籤的差異
- 常見情境-帳號是否重複(異步)
  <!--more-->

# 一般用法

其實這個有點像FormBuilder的零件版,

可單獨使用,不需`[formGroup]`

ip-detail.component.ts
```js
  ipContent = new FormControl("", [
    Validators.required,
    ValidationService.ipValidator
  ]);
```

ip-detail.component.html
```
<div>
    <input [formControl]="ipContent"/>
    <validation-messages [control]="ipContent"></validation-messages>
</div>
<button
    (click)="enter()"
    [disabled]="!ipContent.valid"
    [ngClass]="{ disable: !ipContent.valid }"
>
    {{ "insert" | translate }}
</button>
```

- 驗證方式跟FormBuilder一樣

---

# 有沒有`<form>`標籤的差異

當沒有使用`[formGroup]`時,html寫法也有些變化

```
//FormBuilder
<form [formGroup]="form">
  <input type="text" formControlName="account"/>
  <validation-messages [control]="form.controls.account">
    </validation-messages>
  <button mat-button (click)="enter()" [disabled]="!form.valid" 
          [ngClass]="{ disable: !form.valid }">
    {{ "enter" | translate }}
  </button>
</form>


//FormControl
<input type="text" [formControl]="account"/>
<validation-messages [control]="account"></validation-messages>
<button (click)="submit()" [disabled]="account.invalid" 
[ngClass]="{'disable':account.invalid}">
  {{ "enter" | translate }}
</button>
```

- 有`[formGroup]`時,input控鍵需寫上`formControlName`

---

# 常見情境-帳號是否重複(異步)

實務上要驗證帳號是否重複,
有兩種方式,
- 第一種是使用者邊輸入的時候資料就邊送去後端比對
- 第二種是使用者全部輸入完按送出


## 第一種則是異步,如下例

在 post-detail.component.html
```
<div>
  <input type="text" [formControl]="ipContent"/>
  <validation-messages [control]="ipContent"></validation-messages>
  <button (click)="submit()" [disabled]="ipContent.invalid" 
  [ngClass]="{'disable':ipContent.invalid}">
    {{ "binding" | translate }}
  </button>
</div>
```

在 post-detail.component.ts
```js
createForm() {
    let obj = {
      account: [
        "",
        [
          Validators.required,
          ValidationService.userValidator,
          Validators.minLength(6)
        ], 
        this.asyncValidator.bind(this)
      ],
      password: [
        "",
        [
          Validators.required,
          ValidationService.passwordValidator,
          Validators.minLength(6)
        ]
      ]
    };
    this.form = this.fb.group(obj);
  }

asyncValidator(control): Observable<IDataMain> {
    let obj = this.dataService.setWsModel(
      "self",
      "bindotp",
      "request",
      control.value
    );
    return Observable.create(observer => {
      this.dataService.ObWsModel(obj).subscribe({
        next: result => {
          if (result.errorCode == "0") {
            observer.next(null);
          } else {
            observer.next({ invalidOtpFail: true });
          }
          observer.complete();
        }
      });
    });
  }
```
- 在FormBuilder的物件中,第三個參數是放異步加載的function,

通常回傳的是Observable或是Promise,我個人比較習慣用Observable

- 上述例子是後端資料送回來的errorCode只要不是0,就是有錯誤訊息


## 第二種等使用者全輸入完(非異步)

在 post-detail.component.html
```
<div>
  <input type="text" [formControl]="ipContent"/>
  <validation-messages [control]="ipContent"></validation-messages>
  <button (click)="bind()" [disabled]="ipContent.invalid" 
  [ngClass]="{'disable':ipContent.invalid}">
    {{ "binding" | translate }}
  </button>
</div>
```

在 post-detail.component.ts
```js
createForm() {
    let obj = {
      account: [
        "",
        [
          Validators.required,
          ValidationService.userValidator,
          Validators.minLength(6)
        ]
      ],
      password: [
        "",
        [
          Validators.required,
          ValidationService.passwordValidator,
          Validators.minLength(6)
        ]
      ]
    };
    this.form = this.fb.group(obj);
  }

bind() {
    let obj = this.dataService.setWsModel(
      "self",
      "bindotp",
      "request",
      this.ipContent.value
    );
    this.dataService
      .ObWsModel(obj)
      .pipe(take(1))
      .subscribe(val => {
        if (!val.e) {
          this.data.OTPBinding = true;
        } else {
          this.ipContent.setErrors({ invalidOtpFail: true });
        }
      });
  }
```

------

稍微說明一下setErrors:

一般我們在使用FormBuilder或是FormControl的時候,

第二個參數是以陣列的方式,並塞進要驗證的function

```
account: [
  "",
  [
    Validators.required,
    ValidationService.userValidator,
    Validators.minLength(6)
  ]
],
```

當驗證出`account`的值有錯誤時,其實它會自動幫我們塞setErrors

所以實務上有發生需要自己寫自己塞setErrors,

就是如上例所寫的

```
  this.ipContent.setErrors({ invalidOtpFail: true });
```

當出現錯誤時就會show: `invalidOtpFail`