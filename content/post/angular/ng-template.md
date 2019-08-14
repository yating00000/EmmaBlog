---
date: 2018-09-14 10:42:25
title: ng-template
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
    "angular",
    "ng-template"
]
comment: true
---

- ng-template的使用 -ngFor
- ng-template的使用 -ngIf
<!--more-->

# ng-template的使用

原先的

```html
<tbody>
<tr *ngFor="let element of data; let i = index">
    <td>...<td>
</tr>
</tbody>
```

想再加一行`<tr>`,也要拿 data迴圈資料

```html
<tbody>
<tr *ngFor="let element of data; let i = index">
    <td>...<td>
</tr>
<tr *ngFor="let element of data; let i = index">
    <td>...<td>
</tr>
</tbody>
```

只要使用 ng-template

```html
    <ng-template ngFor let-element [ngForOf]="data" let-i="index">
        <tr>...</tr>
        <tr>...</tr>
    </ng-template>
```

若有想要 ngFor跟ngIf 同時出現,可寫成

```html
    <ng-template [ngIf]="!!result">
        <div *ngFor="let ip of result;let i = index">
            {{ip}}
        </div>
    </ng-template>
```
