---
title: ngx-translate
abbrlink: 4a106377
date: 2018-12-17 09:28:45
categories:
  - angular
tags:
  - angular
  - 實作心得
keywords:
  - angular
  - angular6
  - paginator
  - translate
  - onLangChange
  - items_per_page
  - MatPaginatorIntl
description:
---

- 安裝簡述
- 一般用法
- `<mat-paginator>`的翻譯
<!--more-->

# 安裝簡述
其實網上好多教學,
所以簡單紀錄

```js
npm install @ngx-translate/core --save
npm install @ngx-translate/http-loader --save
```

```js
//app.module.ts

export function createTranslateLoader(http: HttpClient) {
    //i18n的資料夾位置
    return new TranslateHttpLoader(http, "./assets/i18n/", ".json");
}

@NgModule({
  imports: [
    BrowserAnimationsModule, 
    PostModule, 
    HttpClientModule,
    TranslateModule.forRoot({
        loader: {
        provide: TranslateLoader,
        useFactory: createTranslateLoader,
        deps: [HttpClient]
        }
    }), 
    AppRoutingModule
    ],
    declarations: [AppComponent],
    providers: [AppService],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

```js
//share.module.ts

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    TranslateModule
  ],
  exports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    TranslateModule
  ]
})
export class SharedModule { }
```

```js
//app.component.ts

export class AppComponent  {
  defaultLang = "en";

  constructor(public translate: TranslateService){
    translate.addLangs(["en" ,"tw"]);
    translate.setDefaultLang(this.defaultLang);
    translate.use(this.defaultLang);
  }
}
```
---

# 一般用法

- 在需要使用的功能模塊

```js
@Component({
  selector: "app-post",
  template: `
    <p>{{'welcome' | translate}}</p>
    <button (click)="changeTranslate('tw')">繁體</button>
    <button (click)="changeTranslate('en')">英文</button>
  `
})
export class PostComponent implements OnInit {
 
  constructor(public translate: TranslateService) {}

  ngOnInit() {}

  changeTranslate(lang){
    this.translate.use(lang);
  }
}
```

- placeholder

```
<input type="text" [placeholder]="'import_account' | translate">
```

- 可直接在Component裡翻譯字串

```js
constructor(private translate: TranslateService) {}
  
translate(str:string):string{
    return this.translate.instant(str);
}

```

---

# `mat-paginator`的翻譯

接下來紀錄一下在`Angular-Material`的頁碼如何翻譯.

`Paginator`安裝過程請參照官網,

然後在shareModule裡

```js
@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    MatPaginatorModule,
    TranslateModule
  ],
  exports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    MatPaginatorModule,
    TranslateModule
  ]
})
export class SharedModule { }
```

在需要的模塊上使用
```
//post.component.ts

export class PostComponent implements OnInit {
  PAGESIZEOPTIONS=[5,10,15]

  constructor(public translate: TranslateService) {}
  ngOnInit() {}

  setPage($event){
    console.log($event)
  }
}

//post.component.html

<mat-paginator
      [pageIndex]="1"
      [length]="0"
      [pageSize]="10"
      [pageSizeOptions]="PAGESIZEOPTIONS"
      (page)="setPage($event)"
>
</mat-paginator>
```

![](/img/translate_1.png)

那很多功能模塊都會有頁碼,

所以單純的在各個模塊翻譯各自的頁碼是比較不妥的,

加上實務上經常會切換語系,

所以會用到

```js
this.translate.onLangChange.subscribe((e: Event) => {
     //do something
});
```
onLangChange能監聽語言變化,不管哪個模塊切了語系,

其他模塊都會跟著切換.

所以我們客製 MatPaginatorIntl

```js
import {TranslateService} from '@ngx-translate/core';
import {MatPaginatorIntl} from '@angular/material';
import {Injectable} from '@angular/core';

@Injectable()
export class CustomMatPaginatorIntl extends MatPaginatorIntl {
  constructor(private translate: TranslateService) {
    super();

    this.translate.onLangChange.subscribe((e: Event) => {
      this.getAndInitTranslations();
    });

    this.getAndInitTranslations();
  }

  getAndInitTranslations() {
    this.translate.get(['items_per_page', 'next_page', 'previous_page', 'of_label']).subscribe(translation => {
      this.itemsPerPageLabel = translation['items_per_page'];
      this.nextPageLabel = translation['next_page'];
      this.previousPageLabel = translation['previous_page'];
      this.changes.next();
    });
  }

 getRangeLabel = (page: number, pageSize: number, length: number) =>  {
    if (length === 0 || pageSize === 0) {
      return `0 / ${length}`;
    }
    length = Math.max(length, 0);
    const startIndex = page * pageSize;
    const endIndex = startIndex < length ? Math.min(startIndex + pageSize, length) : startIndex + pageSize;
    return `${startIndex + 1} - ${endIndex} / ${length}`;
  }
}
```

```js
//./assets/i18n/tw.json

  "items_per_page": "筆數",
  "next_page": "下一頁",
  "previous_page": "上一頁",
  "of_label": "of",

```

```js
//share.module.ts

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    TranslateModule,
    MatPaginatorModule,
  ],
  exports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    TranslateModule,
    MatPaginatorModule,  
  ],
  providers: [
    {
      provide: MatPaginatorIntl,
      useClass: CustomMatPaginatorIntl
    }
  ]
})
export class SharedModule {}

```

![](/img/translate_2.png)