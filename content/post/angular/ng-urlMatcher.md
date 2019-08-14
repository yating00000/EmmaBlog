---
date: 2018-09-14 11:15:10
title: UrlMatcher
tags: [
  "angular",
  "學習筆記"
]
categories: [
  "angular",
]
keywords: [
    "angular",
    "ng-template"
]
comment: true
---

- UrlMatcher 意義
- UrlMatcher 寫法
- 備註
<!--more-->

# UrlMatcher 意義

就是驗證網址跟參數是否有符合規則


# UrlMatcher 實際運用

在路由中設定

```js
const appRoutes: Routes = [
    {
        path: "enemy",
        component: MyComponent,
        children: [
            {
                matcher: mymatcher,  //寫這個後就不能再寫path
                component: ChildComponent
            }
        ]
    }
];
```

```js
    export const mymatcher = (url: UrlSegment[]): UrlMatchResult => {
        console.log("url", url);
        if (!!url[0] && url[0].toString() !== "bbb") {
            return null;
        }
        return {
            consumed: url,
        };
    };
```

當網址 `enemy/bbb` 將匹配成功

`enemy/aaa`  就會跳出錯誤訊息 `Error: Cannot match any routes.`

# 備註

```js
@NgModule({
    imports: [RouterModule.forChild([
        {
            matcher : matcher, //這裏不要使用匿名方法或則箭頭函數哦, aot 不過
            component : FirstComponent,
            children : []
        }
    ])],
    exports: [RouterModule],
})
```
