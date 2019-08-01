---
title: 路由導向 navigate 
date: 2018-09-14 11:44:26
catalog: true
header-img: route.jpg
id: ng114426
categories:
  - angular
tags:
  - angular
  - 學習筆記
keywords:
  - angular
  - angular6
  - route
  - 路由
description:
abbrlink: 6a98e3b6
---

- 導向的幾種寫法  `Component`
- 導向的其他寫法  `Html`
- navigate()  和  navigateByUrl()  差異
<!--more-->

# 導向的幾種寫法  `Component`
---
以根路由跳轉/login
```
    this.router.navigate(['login']);
```

設置relativeTo相對當前路由跳轉，route是ActivatedRoute的實例，使用需要導入ActivatedRoute

```
    this.router.navigate(['login'],{relativeTo: route});
```

路由中傳參數 /login?name=1

```
    this.router.navigate(['login'],{ queryParams: { name: 1 } });
```

---
preserveQueryParams默認值為false，設為true，是保留之前路由中的查詢參數，
例如: /login?name=1 到 /home?name=1

```
    this.router.navigate(['home'], { preserveQueryParams: true });
```

- `在Angular 4+中，不推薦使用preserveQueryParams,請用queryParamsHandling。`
```
    this.router.navigate(['home'], { queryParamsHandling: "preserve" });
```

- `queryParamsHandling 有三種屬性`
```
    merge:合併已有的 queryParams 到當前的 queryParams 中
    preserve:儲存當前的 queryParams
    default:僅使用查詢參數
```
---

路由中錨點跳轉 /home#top

```
    this.router.navigate(['home'],{ fragment: 'top' });
```

preserveFragment默認為false，設為true，是保留之前路由中的錨點，
例如: /home#top to /role#top

```
    this.router.navigate(['/role'], { preserveFragment: true });
```

skipLocationChange默認為false，設為true，路由跳轉時瀏覽器中的url會保持不變，
但是傳入的參數依然有效

```
    this.router.navigate(['/home'], { skipLocationChange: true });
```

replaceUrl默認為true，設為false，路由不會進行跳轉

```
    this.router.navigate(['/home'], { replaceUrl: true });
```
若要設定兩個參數以上,可這麼寫

```
    this.router.navigate(['/home'], 
    { queryParams: { 'session_id': sessionId },  fragment: 'anchor' });
```

```
    let navigationExtras: NavigationExtras = {
        queryParams: { 'session_id': sessionId },
        fragment: 'anchor'
    };

    this.router.navigate(['/login'], navigationExtras);
```

navigate() 裡的第一個參數 丟路徑跟params相關的`陣列`,
第二個參數是丟其他設定或是queryParams相關的`物件`
---

# 導向的其他寫法  `Html`

```
    <a routerLink="/heroes">Heroes</a>
```

```
    <a [routerLink]="['../']" [queryParams]="{prop: 'xxx'}">Somewhere</a>
```

```
    <a [routerLink]="['/main/manage']" [queryParams]="{'name':"Hello", 'num':10}" [fragment]="yyy">Somewhere</a>
```

```
     <a [routerLink]="['data',{ key : '123' }]" [queryParams]="{ name : 'aaa' }">go child</a>
     // http://somewhere/data;key=123?name=aaa
```
---

# navigate()  和  navigateByUrl()  差異

- navigateByUrl()是進行絕對路徑,絕對路徑必須先`/`

- navigate() 是採用輸入一系列的參數來產生 URL

```
    this.router.navigateByUrl('/login');
    this.router.navigate(['/login']);
```

---
# 備註

- "/path" 表示從 root 開始

- "../path" 表示從當前route往上(parent)

- "path" 表示從當前往下(child) 

---
# 參考至
>- https://hk.saowen.com/a/a921cad029026129e7dc9d0637f0ded431fcaf73311c557d633ba55348f617a8
>- https://hk.saowen.com/a/8d173d70f153930b4c6a1e0bbd60934b1d5425ed83e5909f2c1f3ea5fdd34219
>- https://ifun01.com/8STN9F7.html




