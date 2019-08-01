---
title: 路由簡介3-延遲子路由
date: 2018-10-27 12:34:13
categories:
  - angular
tags:
  - angular
  - 學習筆記
keywords:
  - angular
  - angular6
  - angular route
  - angular 路由
description:
abbrlink: a73d9d82
---

- 路由 - 延遲子路由的寫法
<!--more-->

# 路由的幾種型態

---

- 主路由

- 子路由

- 延遲子路由

- 輔助路由


## 延遲子路由的寫法

```
export const routes: Routes = [
  {
    path: "home",
    loadChildren: "src/app/cms/home/home.module#HomeModule"
  },
];
```

- PS:若寫了延遲子路由,出現`TypeError: __webpack_require__.e is not a function`,
只要把版本升級即可,至少 "@angular/cli": "1.7.3",
- 當時在1.7.2一直錯誤,但後續我改成`loadChildren: () => HomeModule
`就OK


---

注意!下面的寫法在 prod 下無法正常執行

```
    { path: 'home', loadChildren: () => HomeModule }
```

請改成

```
    import HomeModule from 'home-module';

    export function loadHomeModule() {
        return HomeModule;
    }

    export const routes = {
        path: 'home',
        loadChildren: loadHomeModule,
    };
```


