---
title: 路由守衛
abbrlink: 74878da8
date: 2018-12-14 15:19:09
categories:
  - angular
tags:
  - angular
  - 實作心得
keywords:
  - angular
  - angular6
  - route
  - guard
  - Observable
description:
---

- 一般用法
- 以`Observable`方式回傳
<!--more-->

# 一般用法

其實網路上有好多教學文,

我簡單寫個紀錄.

```js
@Injectable()
export class GuardService implements CanActivate {
    constructor(
      private service: AppService,
      public loginService: loginService
    ) {}

  canActivate(): boolean {
    if (!this.loginService.isLogin()) {
      this.service.backLogin();
      return false;
    }
    return true;
  }
}
```

```js
@Injectable()
export class AppService {

  constructor(private router: Router) {}

  backLogin(){
    this.router.navigate(['login']);
  }
}
```

在RouteModule裡

```js
const routes: Routes = [
  {
    path: "cms",
    component: CoreComponent,
    canActivate: [GuardService],
    children: CmsRouting
  },
  { path: "", redirectTo: "cms/home", pathMatch: "full" }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class CoreRoutingModule {}
```

---

# 以`Observable`方式回傳

實務上通常都要等後端送使用者資訊回來,
才能確定裡面內容都可以看,
至於何時送回來呢
guard.service 以訂閱的方式來接

```js
//login service
@Injectable()
export class LoginService {
  private loginSubject = new Subject<boolean>();

  loginState = this.loginSubject.asObservable();

  constructor() {}

  login() {
    this.loginSubject.next(true);
  }

  logout() {
    this.loginSubject.next(false);
  }
}
```


```js
//app component
@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent implements OnInit {
  constructor(private loginService: LoginService) {}
  ngOnInit() {
       this.dataService.ObWsModel(obj).subscribe(val => {
        if (!!val && !!val.Status && val.Status== 1) {
            this.loginService.login();
        } else {
            this.loginService.logout();
        }
    });
  }
}
```

```js
@Injectable()
export class AppService {

  constructor(private router: Router) {}

  backLogin(){
    this.router.navigate(['login']);
  }
}
```

以上述例子來說如果後端送回來的使用者狀態為1,

代表有權限登入,並觸發訂閱

```js
//guard service
@Injectable()
export class GuardService implements CanActivate {
    constructor(
      private service: AppService,
      private loginService: loginService
    ) {}

  canActivate(): Observable<boolean> {
    return this.loginService.loginState
      .map(e => {
        if (e) {
          return true;
        }
      })
      .catch(() => {
        this.service.backLogin();
        return Observable.of(false);
      });
  }
}
```