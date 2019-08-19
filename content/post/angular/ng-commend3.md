---
date: 2018-09-14 10:47:25
title: Angular指令 3 - 結構型
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
  "angular",
  "ng-container",
  "ng-template"
]
comment: true
---

- `<ng-container>` `<ng-template>`
- ngIf 寫法差異
- ngFor 寫法差異
- ngSwicth 寫法差異
  <!--more-->

# `<ng-container>` `<ng-template>`

```html
//一般寫法如下
<div *ngIf="hero" class="name">{{hero.name}}</div>

//被Angular翻譯如下
<ng-template [ngIf]="hero">
  <div class="name">{{hero.name}}</div>
</ng-template>
```

> `*ngIf` 會被自動翻譯成`<ng-template>`來包裹

```html
<!--error-->
<div *ngIf="hero" *ngFor="let hero of heros" class="name">{{hero.name}}</div>

<!--ok 但是可能破壞CSS格局-->
<div *ngIf="hero">
  <div *ngFor="let hero of heros" class="name">{{hero.name}}</div>
</div>
```

> 結構型指令只能在元素中綁定一個

```html
<!--為了不破壞格局 可使用 ng-container-->
<ng-container *ngIf="hero">
  <div *ngFor="let hero of heros" class="name">{{hero.name}}</div>
</ng-container>
```

# ngIf 寫法差異

```html
<div *ngIf="hero" class="name">{{hero.name}}</div>
```

```html
<ng-container *ngIf="hero">
  <div class="name">{{hero.name}}</div>
</ng-container>
```

```html
<ng-template [ngIf]="hero">
  <div class="name">{{hero.name}}</div>
</ng-template>
```

# ngFor 寫法差異

```html
<div *ngFor="let data of datas;let i = index">{{data.id}}</div>
```

```html
<ng-container *ngFor="let data of datas;let i = index">
  <div>{{data.id}}</div>
</ng-container>
```

```html
<ng-template ngFor let-data [ngForOf]="datas" let-i="index">
  <div>{{data.id}}</div>
</ng-template>
```

# ngSwictch 寫法差異

```html
<div [ngSwitch]="value">
  <div *ngSwitchCase="0">one</div>
  <div *ngSwitchCase="1">two</div>
</div>
```

```html
<div [ngSwitch]="value">
  <ng-container *ngSwitchCase="0">one</ng-container>
  <ng-container *ngSwitchCase="1">two</ng-container>
</div>
```

```html
<div [ngSwitch]="value">
  <ng-template [ngSwitchCase]="0">one</ng-template>
  <ng-template [ngSwitchCase]="1">two</ng-template>
</div>
```
