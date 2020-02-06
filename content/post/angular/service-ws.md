---
date: 2018-12-14 16:03:23
title: 使用websocket
tags: [
  "angular",
  "實作心得"
]
categories: [
  "angular",
]
keywords: [
  "angular",
  "angular websocket",
]
comment: true
---

- 一般用法
- 把資訊隱藏的寫法
  <!--more-->

實務上經常用 websocket 來跟後端溝通,
所以簡單紀錄一下我常用的兩個 service

# 一般用法

- web.service.ts : 跟後端連接溝通的地方
- data.service.ts :接收前端要送給後端的數據,
以及把後端的數據傳給有訂閱的 component


```js
//web.service.ts
@Injectable()
export class SocketService {
  private subject: Subject<MessageEvent>;
  private url: string = "";

  constructor() {}

  public setUrl(wsUrl: string, token: string) {
    this.url = `wss://${wsUrl}/${token}`;
  }

  public connect(): Subject<MessageEvent> {
    if (!this.subject) {
      this.subject = this.create();
    }
    return this.subject;
  }

  private create(): Subject<MessageEvent> {
    const ws = new WebSocket(this.url);
    const observable = Observable.create((obs: Observer<MessageEvent>) => {
      ws.onmessage = obs.next.bind(obs);
      ws.onerror = obs.error.bind(obs);
      ws.onclose = obs.complete.bind(obs);
      return ws.close.bind(ws);
    });
    const observer = {
      next: (data: Object) => {
        if (ws.readyState === WebSocket.OPEN) {
          ws.send(JSON.stringify(data));
        }
      }
    };
    return Subject.create(observer, observable).share();
  }
}
```

```js
//data.service.ts
@Injectable()
export class DataService {
    private wsDatas: BehaviorSubject<any>;

    constructor(
        private logger: LoggerService,
        private wsService: WebService
    ) {}

    webConnet() {
         this.wsDatas = <BehaviorSubject<any>>this.wsService.connect()
        .pipe(
            map(response => {
                this.logger.print("response", response);
                const data = JSON.parse(response.data);
                return data;
            })
        );
    }

    sendWs(obj: any) {
        this.logger.print("next ws", obj);
        this.wsDatas.next(obj);
    }

    isWebSocketIn(): Observable<any> {
        return this.wsDatas.asObservable();
    }
}
```

接下來就是開始跟後端連線

```js
//app.component.ts
login(){
    this.dataService.webConnet();
    this.dataService.isWebSocketIn().subscribe(
        (val: Event) => {
            if (val.type == "open") {
                //當ws連接上時,會回傳"open"狀態
                this.sendUser();
            }
        }
    );
}

sendUser() {
    //下面這個訂閱者只要收到一次值就會關閉(take(1))
    this.dataService
        .isWebSocketIn()
        .pipe(
            filter(val => {
                return val.t == "user" && val.m == "one";
            })
        )
        .take(1)
        .subscribe((val: UserMain) => {
            if (!!val.c) {
            this.service.setUser(val);
            }
        });
    this.dataService.sendModel("user", "one", "request");
 }
```

## 接值的方式 1

如上面例子,我如果想請求使用者的資訊

`this.dataService.sendModel("user", "one", "request");`

但是有可能很多的功能模塊都有各自的請求,
比如請求商品列表,客戶列表等

`this.dataService.sendModel("product", "list", "request");`
`this.dataService.sendModel("customer", "list", "request");`

但後端會把你所請求的傳給你,
可是我們要怎麼分辨哪個數據是商品列表,
所以上面的例子用過濾的方式

```js
.pipe(
    filter(val => {
        return val.t == "product" && val.m == "list";
    })
)
.take(1)
```

# 接值的方式 2

其實上面的方式可能不太好,
因為可能要先過濾掉好幾個不是自己這個模塊要的數據,
應該是我 next 出去給後端,它立即性的回應我這個請求,
所以我們可以修改成

```js
//data.service.ts
@Injectable()
export class DataService {
    private wsDatas: BehaviorSubject<any>;
    private selfSubject = new Subject<any>();
    private count = 0;

    constructor(
        private logger: LoggerService,
        private wsService: WebService
    ) {}

    webConnet() {
         this.wsDatas = <BehaviorSubject<any>>this.wsService.connect()
        .pipe(
            map(response => {
                this.logger.print("response", response);
                if (!!this.selfSubject) {
                    this.selfSubject.next(response);
                }
                return response;
            })
        );
    }

    sendWs(obj: any) {
        this.logger.print("next ws", obj);
        this.wsDatas.next(obj);
    }

    isWebSocketIn(): Observable<any> {
        return this.wsDatas.asObservable();
    }

    ObWsModel(obj): Observable<any> {
        const c = this.count++;
        obj.s += c;
        this.logger.print("next ws", obj);
        this.wsDatas.next(obj);
        return this.selfSubject.pipe(
            filter(val => {
                return val.s === obj.s;
            })
        );
    }
}
```

把後端接回來的值,

我另外再用selfSubject去裝,

`count`是辨識碼,

可以確定我next出去 後端回傳的資料是相對應的

# 把資訊隱藏

在檢查前後端數據,通常我們可以打開 F12 開發者工具中,

按下 Network 裡去看 socket 雙方傳送的數據,

但如果要正式上線就不太妥當

所以在開發的時候,

後端使用二進制的方式送來,

而前端傳送過去的也要包成二進制

```js
//web.service.ts
@Injectable()
export class SocketService {
  private subject: Subject<MessageEvent>;
  private url: string = "";

  constructor() {}

  public setUrl(wsUrl: string, token: string) {
    this.url = `wss://${wsUrl}/${token}`;
  }

  public connect(): Subject<MessageEvent> {
    if (!this.subject) {
      this.subject = this.create();
    }
    return this.subject;
  }

  private create(): Subject<MessageEvent> {
    const ws = new WebSocket(this.url);
    const observable = Observable.create((obs: Observer<MessageEvent>) => {
      ws.onopen = obs.next.bind(obs);
      ws.onmessage = e => {
        //讀取後端數據
        const reader = new FileReader();
        reader.onload = () => {
          let r = <string>reader.result;
          const data = JSON.parse(r);
          obs.next(data);
        };
        reader.readAsBinaryString(e.data);
      };

      ws.onerror = obs.error.bind(obs);
      ws.onclose = obs.complete.bind(obs);
      return ws.close.bind(ws);
    });
    const observer = {
      //傳送數據到後端
      next: (data: Object) => {
        if (ws.readyState === WebSocket.OPEN) {
          ws.send(new Blob([JSON.stringify(data)]));
        }
      }
    };
    return Subject.create(observer, observable).share();
  }
}
```

```js
//data.service.ts
webConnet() {
        this.wsDatas = <BehaviorSubject<any>>this.wsService.connect()
    .pipe(
        map(response => {
            this.logger.print("response", response);
            return response;
        })
    );
}
```

接下來打開 F12 開發者工具,就會看不到資料了

![](/img/service_ws1.png)
