---
date: 2018-09-14 10:46:22
title: Angular指令 2 - 屬性型
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
  "angular",
  "ngStyle",
  "ngClass"
]
comment: true
---

- ngStyle的幾種寫法
- ngClass的幾種寫法
<!--more-->

# ngStyle

後面要指定的參數通常是 `CSS屬性`

```html
<div [ngStyle]="{'background-color':'green'}"></<div>
```

也可以這樣寫

```html
<div [style.background]="green}"></<div>
```

也可以使用三元運算子

```html
<div [ngStyle]="{'background-color':person.country === 'UK' ? 'green' : 'red' }"></<div>
```

若要指定 `function` 的寫法

```html
[style.color]="getColor(person.country)"
```

```js
    getColor(color:string){
        if(color == 'UK'){
            return 'green'
        }
        return 'red'
    }
```

# ngClass

第一个參數為Class類名稱，
第二个參數為boolean值，
如果為true就添加第一个参數的類

```html
[ngClass]="{'text-success':true}"
```

也可以這樣寫

```html
[class.text-success]="person.country === 'UK'"
```

可以一次新增多Class

```html
    [ngClass]="{
        'text-success':person.country === 'UK',
        'text-primary':person.country === 'USA',
        'text-danger':person.country === 'HK'
    }"
```