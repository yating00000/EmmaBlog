---
date: 2018-12-11 17:38:48
title: 表單驗證3-FormBuilder
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
    "angular formbuilder",
    "angular FormControl",
    "angular formGroup",
    "angular formControlName"
  ]
comment: true
---

- 一般用法
- 把驗證方法封裝跟使用
- 常見情境-確認密碼 Confirm Password
  <!--more-->

# 一般用法

- 在 detail.component.html

> 在使用 FormBuilder 的情況下,`<form>`標籤一定要寫,
並標註`[formGroup]`

```html
<form [formGroup]="form">
  //account
  <div>
    <input type="text" formControlName="account"/>
  </div>
  <validation-messages [control]="form.controls.account">
  </validation-messages>

  //passowrd
  <div>
    <input type="text" formControlName="passowrd"/>
  </div>
  <validation-messages [control]="form.controls.passowrd">
  </validation-messages>

  <button mat-button (click)="enter()" 
      [disabled]="!form.valid"
      [ngClass]="{ disable: !form.valid }">
      {{ "enter" | translate }}
  </button>
</form>
```

- 在 detail.component.ts

```js
export class DetailComponent implements OnInit {
  form: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.createForm();
  }

  createForm() {
    let obj = {
      skype: ["", [Validators.required]],
      phone: [""],
    };
    this.form = this.fb.group(obj);
  }
}
```
> 第一個參數則是預設值,第二個參數是檢驗器並以陣列呈現,
（第三個參數是動態驗證的檢驗器可不填）
常見的必填`required`或 長度`minLength`以陣列方式放在第二個參數



## 改變預設值

- 第一種方式就是在創建的時候寫好

```js
let obj = {
  skype: ["123456", [Validators.required]],
  phone: ["123456"]
};
```

- 但常見的情況是在更新資料時接到從後端傳回的封包才能塞值

```js
//this.data => 後端送的值
ngOnInit() {
  this.createForm();
  if (!!this.data) {
    this.initSetForm(this.data);
  }
}

createForm() {
  let obj = {
    skype: ["", [Validators.required]],
    phone: [""],
  };
  this.form = this.fb.group(obj);
}

initSetForm(data) {
  if (!!data) {
    this.form.setValue({
      skype: data.skype || "",
      phone: data.phone || "",
    });
  }
}
```

# 把驗證方法封裝跟使用

因為表單欄位需要驗證的地方很多,
所以我們會做 service 來裝驗證的方法

- 在 validation.service.ts

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

- 在 detail.component.ts

```js
createForm() {
    let obj = {
      skype: ["", [Validators.required]],
      phone: [""],
      account = ["",
          [
            Validators.required,
            ValidationService.userValidator,
            Validators.minLength(6)
          ]
        ],
      password = ["",
          [
            Validators.required,
            ValidationService.passwordValidator,
            Validators.minLength(6)
          ]
        ]
      };
    }
    this.form = this.fb.group(obj);
  }
```

- 在 detail.component.html

```html
//account
<div>
  <input type="text" formControlName="account"/>
</div>
<validation-messages [control]="form.controls.account">
</validation-messages>

//passowrd
<div>
  <input type="text" formControlName="passowrd"/>
</div>
<validation-messages [control]="form.controls.passowrd">
</validation-messages>
```

# 常見情境-確認密碼 Confirm Password

- 在 validation.service.ts

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

  static matchingPasswords(otherControlName: string) {
    let thisControl: FormControl;
    let otherControl: FormControl;

    return function matchOtherValidate(control: FormControl) {
      if (!control.parent) {
        return null;
      }
      if (!thisControl) {
        thisControl = control;
        otherControl = control.parent.get(otherControlName) as FormControl;
        if (!otherControl) {
          throw new Error(
            "matchingPasswords(): other control is not found in parent group"
          );
        }
        otherControl.valueChanges.subscribe(() => {
          thisControl.updateValueAndValidity();
        });
      }
      if (!otherControl) {
        return null;
      }
      if (otherControl.value !== thisControl.value) {
        return { invalid_repeat_password: true };
      }
      return null;
    };
  }
}
```

- 在 user-detail.component.ts

```js
createForm() {
    let obj = {
      oldP: ["",
        [
          Validators.required,
          ValidationService.passwordValidator,
          Validators.minLength(6)
        ]
      ],
      newP: ["",
        [
          Validators.required,
          ValidationService.passwordValidator,
          Validators.minLength(6)
        ]
      ],
      repeatP: ["",
        [Validators.required, ValidationService.matchingPasswords("newP")]
      ]
    };
    this.form = this.fb.group(obj);
  }

```
