---
title: angular module概念
date: 2018-09-14 11:24:46
categories:
  - angular
tags:
  - angular
  - 學習筆記
keywords:
  - angular
  - angular6
  - 模塊
description:
abbrlink: 3da33677
---

- 模塊的多種劃分
- 模塊的匯入順序
<!--more-->

# 模塊的多種劃分
---
- 根模塊: 負責全局的路由

- 核心模塊: 負責全局的Service,也可以定義只在根模塊使用的組件,並只能由根模塊引入一次且不再導出

- 共享模塊是定義全局共享的組件,幫助子模塊導入系統模塊,所以子模塊只要導入共享模塊就夠了

- 子模塊內部可以細分自己的子路由跟子組件,以及提供自己的服務等

- 除了頁面入口模塊(非根模塊)之外的其他子模塊盡量寫成惰性加載的模塊

- 當需要一個通用的全局服務時,可以加入到核心模塊,也可以再創建一個只給根模塊引入的特性模塊,甚至可以獨立發布到npm,但需要更強的編碼能力和技術積累了


# 模塊的匯入順序
---
```
    imports: [
      BrowserModule,
      FormsModule,
      HeroesModule,
      AppRoutingModule
    ]
```

看看該模塊的 imports 數組。注意，AppRoutingModule 是最後一個。<br>
最重要的是，它位于 HeroesModule 之後。


- 如果更換順序的話,如下

```
    imports: [
      BrowserModule,
      FormsModule,
      AppRoutingModule,
      HeroesModule
    ]
```

例如, 如果你 app-routing 最後是處理 404 ,<br>
但是在 app-module 卻把 routing 限於特性模塊 HeroesModule, <br>
那麼 HeroesModule 的 routing 就進不去了。因為已經被匹配掉了.



# 參考至
--- 
>`<https://www.cnblogs.com/yitim/p/angular2-study-module-framework.html>`



