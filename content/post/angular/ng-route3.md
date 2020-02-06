---
date: 2018-10-27 12:34:13
title: 路由3 - 延遲子路由
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
  "angular",
  "angular route",
  "angular 路由",
  "angular 子路由",
  "angular 延遲子路由",
  "angular 輔助子路由"
]
comment: true
---

- 延遲子路由寫法
- 延遲子路由注意事項

<!--more-->

# 延遲子路由寫法

```js
const routes: Routes = [
  {
    path: "core",
    component: CoreComponent,
    children: [
      {
        path: "home",
        loadChildren: "src/app/cms/home/home.module#HomeModule"
      }
    ]
  },
  { path: "", redirectTo: "core", pathMatch: "full" }
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
```

# 延遲路由注意事項

- PS:若寫了延遲子路由,出現`TypeError: __webpack_require__.e is not a function`,
  只要把版本升級即可,至少 "@angular/cli": "1.7.3"

- 若堅持要在 1.7.2 ,請改成`loadChildren: () => HomeModule`就能執行

- 注意!以下在 prod 下無法正常執行

```js
export const routes = {
  path: "home",
  loadChildren: () => HomeModule
};
```

請改成

```js
import HomeModule from "home-module";

export function loadHomeModule() {
  return HomeModule;
}

export const routes = {
  path: "home",
  loadChildren: loadHomeModule
};
```
