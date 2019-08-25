---
date: 2018-10-27 11:34:13
title: 路由2 - 子路由
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
  "angular",
  "route",
  "路由",
  "子路由",
  "延遲子路由",
  "輔助子路由",
  "路由模塊",
  "子組件"
]
comment: true
---

- 子組件
- 子路由
- 延遲子路由
- 功能模塊

寫法上的差異
<!--more-->

# (一) `ChildComponent`

- 檔案結構

```
app
    - child
            - child.component.css
            - child.component.html
            - child.component.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```

## 子組件

- `app-routing.module.ts`

 none

- `app.component.html`

```html
<app-child></app-child>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [...],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

## 子路由

- `app-routing.module.ts`

```js
const routes: Routes = [
  { path: 'child', component: ChildComponent }
];
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

- `app.component.html`

```html
<router-outlet></router-outlet>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [...],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

# (二)`ChildComponent` `ChildModule`

- 檔案結構

```
app
    - child
            - child.component.css
            - child.component.html
            - child.component.ts
            - child.module.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```

## 子組件

- `app-routing.module.ts`

none

- `app.component.html`

```html
<app-child></app-child>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [...],
  imports: [ChildModule],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

- `child.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [...],
  providers: [...],
  exports: [ChildComponent]
})
export class ChildModule {}
```

## 子路由

- `app-routing.module.ts`

```js
const routes: Routes = [
  { path: 'child', component: ChildComponent }
];
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

- `app.component.html`

```html
<router-outlet></router-outlet>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [...],
  imports: [ChildModule, AppRoutingModule],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```


- `child.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [...],
  providers: [...],
  exports: [...]
})
export class ChildModule {}
```


## 功能模塊

> 通常ShareModule就是如此,
或是私有的service 或 指令 混合成的功能組件,
可方便其他module 匯入來用

- `app-routing.module.ts`

none

- 任何要使用的html

```html
<app-child></app-child>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [...],
  imports: [ChildModule],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

- `child.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [...],
  providers: [...],
  exports: [ChildComponent]
})
export class ChildModule {}
```

# (三)`ChildComponent` `ChildModule` `ChildRoute`

- 檔案結構

```
app
    - child
            - child-routing.module.ts
            - child.component.css
            - child.component.html
            - child.component.ts
            - child.module.ts
    - app.component.html
    - app.component.ts
    - app-routing.module.ts
    - app.module.ts
```
> 若有設定自己的路由模塊,就不可能是子組件跟功能模塊了

>以下分類 `子路由與延遲子路由`


## 子路由

- `app-routing.module.ts`

```js
const routes: Routes = [];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

- `app.component.html`

```html
<router-outlet></router-outlet>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [...],
  imports: [ChildModule,AppRoutingModule],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

- `child-routing.module.ts`

```js
const routes: Routes = [
  {
    path: 'child',
    component:ChildComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ChildRoutingModule {}
```

- `child.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [ChildRoutingModule],
  providers: [...],
  exports: [...]
})
export class ChildModule {}
```

## 延遲子路由

- `app-routing.module.ts`

```js
const routes: Routes = [
  {
    path: 'child',
    loadChildren: 'src/app/child/child.module#ChildModule'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

- `app.component.html`

```html
<router-outlet></router-outlet>
```

- `app.module.ts`

```js
@NgModule({
  declarations: [...],
  imports: [AppRoutingModule],
  providers: [...],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

- `child-routing.module.ts`

```js
const routes: Routes = [
  {
    path: '',
    component:ChildComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ChildRoutingModule {}
```

- `child.module.ts`

```js
@NgModule({
  declarations: [ChildComponent],
  imports: [ChildRoutingModule],
  providers: [...],
  exports: [...]
})
export class ChildModule {}
```



