---
date: 2018-09-12 16:44:13
title: angular-cli 相關指令
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords:
  [
    "angular",
    "angular spec",
    "angular cli",
  ]
comment: true
---

- CLI指令
- 如何新增檔案卻不新增 spec 的幾種方式
<!--more-->

# CLI指令

```js
ng generate component heroes //ng g c heros
```

```js
ng generate service hero  //ng g s hero
```

```js
ng generate service hero --module=app 
```

```js
ng generate module app-routing --flat --module=app 
 //ng g m app-routing --flat --module=app 
```

- --flat 不要將 AppRouting 建立在目前目錄下，而是建立在 src/app 目錄下 

- --routing  一併建立 AppRoutingModule

- --module=app 則是告知CLI註冊這個Routing在AppModule的imports裡.

# 如何新增檔案卻不新增 spec 的幾種方式

## Angular2~5

第一種

```js
ng g c component-name --spec false
```

第二種

```js
ng generate component --spec=false component-name
```

第三種   `在angular-cli.json`

```js
    "defaults": {
        "spec": {
            "component": false
        }
    }
```

第四種   `在angular-cli.json`

```js
    "defaults": {
        "component": { "spec": false },
        "service": { "spec": false },
    }
```

## Angular6+

`angular-cli.json` 變更成 `angular.json`

在 `angular.json` 中

```js
   "schematics": {
    "@schematics/angular:component": {
      "prefix": "fmyp",
      "styleext": "css",
      "spec": false
    },
    "@schematics/angular:directive": {
      "prefix": "fmp",
      "spec": false
    },
    "@schematics/angular:module": {
      "spec": false
    },
    "@schematics/angular:service": {
      "spec": false
    },
    "@schematics/angular:pipe": {
      "spec": false
    },
    "@schematics/angular:class": {
      "spec": false
    }
  }
```

or

```
ng generate c mycomponent --no-spec
```

# 參考至
> https://stackoverflow.com/questions/50193766/generating-components-without-spec-ts-in-angular-6?rq=1

