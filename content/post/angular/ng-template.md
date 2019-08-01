---
title: ng-template
date: 2018-09-14 10:42:25
categories:
  - angular
tags:
  - angular
  - 學習筆記
keywords:
  - angular
  - angular6
  - ng-template
description:
abbrlink: dbe2b6c5
---

- ng-template的使用 -ngFor
- ng-template的使用 -ngIf
<!--more-->

# ng-template的使用
---
原先的

```bash
    <tbody>
    <tr *ngFor="let element of data; let i = index">
       <td>...<td>
    </tr>
    </tbody>
```

想再加一行`<tr>`,也要拿 data迴圈資料

```bash
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

```bash
    <ng-template ngFor let-element [ngForOf]="data" let-i="index">
        <tr>...</tr>
        <tr>...</tr>
    </ng-template>
```

若有想要 ngFor跟ngIf 同時出現,可寫成

```bash
    <ng-template [ngIf]="!!result">
        <div *ngFor="let ip of result;let i = index">
            {{ip}}
        </div>
    </ng-template>
```
