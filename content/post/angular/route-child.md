---
title: 路由簡介2-子路由
date: 2018-10-27 11:34:13
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
abbrlink: d03aad14
---

- 路由 - 子路由的寫法
<!--more-->

# 路由的幾種型態 - 子路由

---

- 主路由

- 子路由

- 延遲子路由

- 輔助路由


子路由通常有幾種寫法,而且容易跟其他用途混淆,
拿兩點做標記來分類
- 有無自己的module
- 有無自己的RouteModule

---

## 沒有自己的module && 沒有自己的RouteModule

- ### 有在AppModule匯入

`資料夾`
`home沒有 home.module.ts && 也沒有 home-routing.module.ts`

```
app
    - home
            - home.component.css
            - home.component.html
            - home.component.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```
`AppRoutingModule`
```
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
```
`AppModule`
```
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
`App Html`
```
    <p>
        App works!
    </p>
<router-outlet></router-outlet>
```

-----------------

- ### 沒在AppRoutingModule匯入 => 其實這是子組件

`資料夾`(不變)
```
app
    - home
            - home.component.css
            - home.component.html
            - home.component.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```

`AppRoutingModule`***
```
const routes: Routes = [];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
```
`AppModule`(不變)
```
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
`App Html`***
```
    <p>
        App works!
    </p>
<app-home></app-home>
```

> 差異在於有無寫在 AppRoutingModule 裡

---

## 有自己的module

- ### 有自己的RouteModule

`資料夾`
```
app
    - post
            - post.component.css
            - post.component.html
            - post.component.ts
            - post-routing.module.ts
            - post.module.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```

`AppRoutingModule`***
```
const routes: Routes = [
  {
    path: "post",
    component: PostComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
```
`PostRoutingModule`***
```
const routes: Routes = [
  {
    path: "",
    component: PostComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
```
`PostModule`
```
@NgModule({
  imports: [
    CommonModule,
    PostRoutingModule
  ],
  declarations: [PostComponent],
  exports:[PostComponent]
})
```

`AppModule`
```
@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    PostModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`App Html`***
```
    <p>
        App works!
    </p>
<router-outlet></router-outlet>
```

--------------------------------

- ### 沒有自己的RouteModule => 跟路由無關,通常是拿來做共用模塊的

`資料夾`
```
app
    - post
            - post.component.css
            - post.component.html
            - post.component.ts
            - post.service.ts
            - post.module.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```

`AppRoutingModule` 沒有RouteModule就已跟路由無關了
```
const routes: Routes = [];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
```

`PostModule` 
```
@NgModule({
  imports: [
    CommonModule
  ],
  declarations: [PostComponent],
  exports:[PostComponent]
})
```

`AppModule`
```
@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    PostModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`App Html` 已跟路由無關了
```
    <p>
        App works!
    </p>
```

> 通常ShareModule就是如此,
或是私有的service 或 指令 混合成的功能組件,
可方便其他module 匯入來用