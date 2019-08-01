---
title: ng-directive
date: 2018-09-14 11:04:22
categories:
  - angular
tags:
  - angular
  - 學習筆記
keywords:
  - angular
  - angular6
  - ngStyle
  - ngClass
description: 
abbrlink: ad6bc72f
---

- ngStyle的幾種寫法
- ngClass的幾種寫法
<!--more-->

# ngStyle
---
後面要指定的參數通常是 `CSS屬性`

```
    <div [ngStyle]="{'background-color':'green'}"></<div>
```

也可以這樣寫

```
    <div [style.background]="green}"></<div>
```

也可以使用三元運算子

```
    <div [ngStyle]="{'background-color':person.country === 'UK' ? 'green' : 'red' }"></<div>
```

若要指定 `function` 的寫法

```
    [style.color]="getColor(person.country)"

    getColor(color:string){
        if(color == 'UK'){
            return 'green'
        }
        return 'red'
    }
```


# ngClass
---
第一个參數為Class類名稱，
第二个參數為boolean值，
如果為true就添加第一个参數的類

```
    [ngClass]="{'text-success':true}"
```

也可以這樣寫

```
    [class.text-success]="person.country === 'UK'"
```

可以一次新增多Class

```
    [ngClass]="{
        'text-success':person.country === 'UK',
        'text-primary':person.country === 'USA',
        'text-danger':person.country === 'HK'
    }"
```