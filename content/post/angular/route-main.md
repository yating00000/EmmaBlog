---
title: 路由簡介1-主路由
date: 2018-10-27 10:34:13
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
abbrlink: 4933fcae
---

- 路由 - 主路由的寫法
<!--more-->

# 路由的幾種型態 - 主路由

---

- 主路由

- 子路由

- 延遲子路由

- 輔助路由

---

## 可直接在 AppModule 直接寫

```js
const routes: Routes = [
  {
    path: "",
    redirectTo: "home",
    pathMatch: "full"
  },
  {
    path: "home",
    component: HomeComponent
  }
];
@NgModule({
  imports: [
    RouterModule.forRoot(routes)
  ]
})
export class AppModule { }
```
---

## 可拆到 AppRouteModule ,並在 AppModule 註冊

`AppRoutingModule`

```js
const routes: Routes = [
  {
    path: "",
    redirectTo: "home",
    pathMatch: "full"
  },
  {
    path: "home",
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

`AppModule`

```js
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
