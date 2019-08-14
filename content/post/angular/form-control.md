---
date: 2018-12-12 15:15:57
title: 表單驗證4-FormControl
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
    "form",
    "validator",
    "formbuilder",
    "FormControl",
    "formGroup",
    "formControlName"
  ]
comment: true
---

- 一般用法
- 有沒有`<form>`標籤的差異
- 常見情境-帳號是否重複(異步)
  <!--more-->

# 一般用法

> 其實這個有點像FormBuilder的零件版,
可單獨使用,不需`[formGroup]`

- ip-detail.component.ts

```js
  ipContent = new FormControl("", [
    Validators.required,
    ValidationService.ipValidator
  ]);
```

- ip-detail.component.html

```html
<div>
    <input [formControl]="ipContent"/>
    <validation-messages [control]="ipContent">
    </validation-messages>
</div>
<button
    (click)="enter()"
    [disabled]="!ipContent.valid"
    [ngClass]="{ disable: !ipContent.valid }"
>
    {{ "insert" | translate }}
</button>
```

> 驗證方式跟FormBuilder一樣


# 有沒有`<form>`標籤的差異

> 當沒有使用`[formGroup]`時,html寫法也有些變化

```html
//FormBuilder
<form [formGroup]="form">
  <input type="text" formControlName="account"/>
  <validation-messages [control]="form.controls.account">
  </validation-messages>
  <button mat-button (click)="enter()" 
        [disabled]="!form.valid" 
        [ngClass]="{ disable: !form.valid }">
    {{ "enter" | translate }}
  </button>
</form>
```

```html
//FormControl
<input type="text" [formControl]="account"/>
<validation-messages [control]="account"></validation-messages>
<button (click)="submit()" 
        [disabled]="account.invalid" 
        [ngClass]="{'disable':account.invalid}">
  {{ "enter" | translate }}
</button>
```

> 有`[formGroup]`時,input控鍵需寫上`formControlName`

# 常見情境-帳號是否重複(異步)

實務上要驗證帳號是否重複,
有兩種方式:

- 第一種是使用者邊輸入的時候資料就邊送去後端比對
- 第二種是使用者全部輸入完按送出


## 第一種則是異步,如下例

- 在 post-detail.component.html

```html
<div>
  <input type="text" [formControl]="ipContent"/>
  <validation-messages [control]="ipContent"></validation-messages>
  <button (click)="submit()" 
          [disabled]="ipContent.invalid" 
          [ngClass]="{'disable':ipContent.invalid}">
    {{ "binding" | translate }}
  </button>
</div>
```

- 在 post-detail.component.ts

```js
createForm() {
    let obj = {
      account: ["",
        [
          Validators.required,
          ValidationService.userValidator,
          Validators.minLength(6)
        ], 
        this.asyncValidator.bind(this)
      ],
      password: ["",
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
      "request",
      control.value
    );
    //後端資料送回來的errorCode只要不是0,就是有錯誤訊息
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
> 在FormBuilder的物件中,第三個參數是放異步加載的function,

> 通常回傳的是Observable或是Promise,我個人比較習慣用Observable


## 第二種等使用者全輸入完(非異步)

- 在 post-detail.component.html

```html
<div>
  <input type="text" [formControl]="ipContent"/>
  <validation-messages [control]="ipContent"></validation-messages>
  //點擊後驗證
  <button (click)="onBind()" 
          [disabled]="ipContent.invalid" 
          [ngClass]="{'disable':ipContent.invalid}">
    {{ "binding" | translate }}
  </button>
</div>
```

- 在 post-detail.component.ts

```js
createForm() {
    let obj = {
      account: ["",
        [
          Validators.required,
          ValidationService.userValidator,
          Validators.minLength(6)
        ]
      ],
      password: ["",
        [
          Validators.required,
          ValidationService.passwordValidator,
          Validators.minLength(6)
        ]
      ]
    };
    this.form = this.fb.group(obj);
  }

onBind() {
    let obj = this.dataService.setWsModel(
      "request",
      this.ipContent.value
    );
    this.dataService
      .ObWsModel(obj)
      .pipe(take(1))
      .subscribe(val => {
        if (!val.errorcode) {
          this.data.OTPBinding = true;
        } else {
          this.ipContent.setErrors({ invalidOtpFail: true });
        }
      });
  }
```

### setErrors是什麼

- 假設是FormBuilder 或是 FormControl

```js
account: [
  "",
  [
    Validators.required,
    ValidationService.userValidator,
    Validators.minLength(6)
  ]
],
```

> 當驗證出`account`的值有錯誤時,

> 其實它會自動幫我們塞setErrors`{required:true}`

- 可如果是按鈕點擊驗證,就需要自己塞setErrors

```js
  this.ipContent.setErrors({ invalidOtpFail: true });
```

> 當出現錯誤時就會show: `invalidOtpFail`