---
date: 2018-10-27 10:34:13
title: 路由1 - 簡介
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

- 主路由
- 子路由
- 延遲子路由
- 輔助子路由

<!--more-->

# 主路由

## 可直接在 AppModule 直接寫

```js
const routes: Routes = [
  { path: '',   redirectTo: '/index', pathMatch: 'full' }
];
@NgModule({
  imports: [
    RouterModule.forRoot(routes)
  ]
  ...
})
export class AppModule { }
```

## 可拆到 AppRouteModule ,並在 AppModule 註冊

- `AppRoutingModule`

```js
const routes: Routes = [
  { path: '',   redirectTo: '/index', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

- `AppModule`

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

# 子路由

```js
const routes: Routes = [
  { path: 'home', component: HomeComponent }
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class HomeRoutingModule { }
```

# 延遲子路由

```js
const routes: Routes = [
  {
    path: "home",
    loadChildren: "src/app/cms/home/home.module#HomeModule"
  },
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
```

# 輔助子路由

```js
const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'talk', component: AuxComponent, outlet: 'aux' }
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
```

- 在`app.component.html`

```html
<a [routerLink]="[{ outlets: { 'aux': ['talk'] } }]">Aux</a>
```