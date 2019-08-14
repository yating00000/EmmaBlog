---
date: 2018-09-14 10:49:17
title: ngModel
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
    "angular",
    "ngModel"
]
comment: true
---

- ngModel 幾種寫法
- 在 `<select>` 裡的差異
<!--more-->

# 寫法

```js
[(ngModel)]="personalEmail"
```

```js
[ngModel]="personalEmail" 
(ngModelChange)="personalEmail = $event"
```

```js
[ngModel]="personalEmail || 'default'" 
(ngModelChange)="personalEmail = $event"
```

# 在 `<select>` 裡的差異

```html
<select id="se" [ngModel]="seID" (ngModelChange)="seID = $event">
    <option *ngFor="let s of ss" [value]="s.id">{{ s.name }}</option>
</select>
```

因Angular 內部會自己將處理 `$event.target.value`, 

所以可合一 `[(ngModel)]="seID"`, 如下所示

```html
<select id="se" [(ngModel)]="seID">
    <option *ngFor="let s of ss" [value]="s.id">{{ s.name }}</option>
</select>
```

如果不單只是數據變動還要做其他處理時,可用 `(change)`

```html
<select id="se" [(ngModel)]="seID" (change)="saveStatus(member.ID,$event)">
    <option *ngFor="let s of ss" [value]="s.id">{{ s.name }}</option>
</select>
```

可在`(change)`指令下自訂義function, 但要注意接進來的 $event 並未被angular內部處理

```js
saveStatus(id: number, event) {
    let o = {
        ID: id,
        status: parseInt(event.target.value)
    };
    this.dataService.sendModel("customer", "status", "", o);
}
```