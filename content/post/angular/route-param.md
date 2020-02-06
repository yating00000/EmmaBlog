---
date: 2018-09-14 11:42:29
title: 路由參數 params queryParams
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
    "angular params",
    "angular queryParams"
]
comment: true
---

- params 與 queryParams 區分
- URL 表達形式
- 讀取 params 的幾種寫法
- 讀取 queryParams 的寫法
- 讀取 fragment 的幾種寫法
<!--more-->

# params 與 queryParams 區分

```
//Sample URL
http://somewhere/heroes?paramA=1
http://somewhere/enemy?paramA=1
```

```js
//路由設定
export const MyRoutes: Routes = [
    {path: ':type', component: MyComponent}
];
```

```js
//讀取
this.paramsSubscription = this.route.params.subscribe((param: any) => {
    console.log('type',param['type']);
    //會印出 heroes or enemy

    let q = this.route.snapshot.queryParams["paramA"];
    console.log('queryParams',q)
    //會印出 1
})
```

在`?`前面即為 params, 在`?`後面即為 queryParams

```
//Sample URL
http://somewhere/heroes/5?paramA=1
http://somewhere/enemy/3?paramA=1
```

```js
//路由設定
export const MyRoutes: Routes = [
    {path: ':type', component: MyComponent}
    {path: ':type/:id', component: MyComponent}
];
```

```js
//讀取
this.paramsSubscription = this.route.params.subscribe((param: any) => {
    console.log('type',param['type']);
    console.log("type", param["id"]);
    //會印出 heroes 5
    // or
    // 印出 enemy 3
})
```

由上面兩個例子可以看到`?前面`的`每個/`後面都是params


# URL 表達形式

- 帶有查詢參數的URL： http://somewhere/heroes/5?paramA=1&paramB=6542

    params: `heroes/5`

    queryParams: `paramA=1&paramB=6542`

- 帶有矩陣參數的URL： http://somewhere/enemy/3;paramA=1;paramB=6542

    params: `enemy/3;paramA=1;paramB=6542`

    queryParams: `無`

# 讀取 params 的幾種寫法

## 訂閱方式 Observable
```js
this.activatedRoute.params.subscribe((param: any) => {
    console.log('type',param['type']);
})
```

## 快照 Snapshot
```js
this.activatedRoute.snapshot.params
```

```js
this.activatedRoute.snapshot.params['type']
```

```js
const id = +this.route.snapshot.paramMap.get('id');
```

舉個例子:

```
//Sample URL
http://somewhere/heroes/5?paramA=1&paramB=6542

//印出 params
console.log(this.activatedRoute.snapshot.params)

//結果
{type: "heroes", id: "5"}
```

舉個例子:

```
//Sample URL
http://somewhere/enemy/3;paramA=1;paramB=6542

//印出 params
console.log(this.activatedRoute.snapshot.params)

//結果
{type: "enemy", id: "3", paramA: "1", paramB: "6542"}
```

# 讀取 queryParams 的寫法

## 訂閱方式 Observable
```js
this.activatedRoute.queryParams.subscribe((param: any)=>{
    console.log('paramA',param['paramA']);
});
```

## 快照 Snapshot

```js
this.activatedRoute.snapshot.queryParams;
```

舉個例子:

```
//Sample URL
http://somewhere/heroes/5?paramA=1&paramB=6542

//印出 params
console.log(this.activatedRoute.snapshot.queryParams)

//結果
{paramA: "1", paramB: "6542"}
```

舉個例子:

```
//Sample URL
http://somewhere/enemy/3;paramA=1;paramB=6542

//印出 params
console.log(this.activatedRoute.snapshot.queryParams)

//結果
{}
```

# 讀取 fragment 的幾種寫法

fragment 指網址`#`後面的參數

## 訂閱方式 Observable

```js
this.activatedRoute.fragment.subscribe((f))=>{
    console.log('f',f);
});
```

## 快照 Snapshot

```js
this.activatedRoute.snapshot.fragment;
```


# 備註

因網址傳送跟接收都是用字串,所以如果在讀取`params`,

在常見的抓取`:id` 的時候我們會用 `+` 號來讓它字串自動轉成數字

```js
this.activatedRoute.params.subscribe((param: any) => {
    console.log('type',+param['type']);   //多了+號
})
```

- ActivatedRoute 可以不需要 unsubscribe，這一個 ng 會智能處理，不過養成取消訂閲的習慣也是很好的

- 如果觸發一個 超連結 或則調用 router.navigate(...) 但是最終它發現 url 沒變動，那麼什麼不會發生, route event 統統沒有運行. 

