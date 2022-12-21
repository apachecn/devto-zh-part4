# 角度和节点:使用服务器发送的事件进行下载

> 原文：<https://dev.to/elasticrash/angular-node-using-server-sent-events-for-downloads-29em>

## 让我们从解决方案的角面开始

下面两个代码片段是同一个角度服务的一部分。这里，函数`connect`创建一个新的 EventSource，并将它从后端 API 收到的所有消息转发给一个观察器。

```
public eventSource;
public connect(url): Observable<any> {
    return Observable.create((observer) => {
      const es = new EventSource(url);

      this.eventSource = es;
      es.onmessage = (event) => {
        this._zone.run(() => observer.next(
          event.data
        ));
      };

      es.onerror = (error) => {
        observer.error(error);
      };
    });
  } 
```

接下来，我创建了两个任意的缩写`EOS`(流的结尾)和`BOS`(流的开始)，老实说，这并不是必需的，但有时很有用，尤其是当后端运行长时间运行的查询时。通过立即发送`BOS`,您可以让客户端在请求的那一刻接收到响应头。

然后，我组合消息中的数据，使用一个老技巧来触发下载(创建一个 html 元素，然后单击它)。

```
private beginningOfStream: string = "BOS"
private endOfStream: string = "EOS"

 public async stream(url:string): Promise<any> {
    const filename = `export_${moment().format('L').replace(/\//g, "")}_${moment().format('HHmm')}.csv`;

    return new Promise(async (resolve, reject) => {
      try {
        let data = '';
        this.connect(url).subscribe((response) => {
          switch (response) {
            case this.beginningOfStream:
              break;
            case this.endOfStream:
              const blob = new Blob([data], { type: 'application/txt' });
              const url = window.URL.createObjectURL(blob);
              const a = document.createElement('a');
              a.href = url;
              a.download = filename;
              document.body.appendChild(a);
              a.click();
              this.end();
              resolve({ info: 'success' });
              break;
            default:
              data += JSON.parse(response);
          }
        }, (error) => {
          if (this.eventSource) {
             this.eventSource.close();
          }
          reject(error);
        });
      } catch (error) {
        console.log(`Error occurred: ${error}`);
        if (this.eventSource) {
           this.eventSource.close();
        }
        reject(error);
      }
    });
  } 
```

## 并以我们解的节点端结束

这是我的快递路线样本。现在我使用 Express+Typescript 组合的方式有点笨拙，但它工作得很好。也许那会成为另一篇好文章。

但是，在一天结束的时候，我想要达到的目标是很明显的。我正在创建事件流的头部，并通过保持连接活动将消息发送回客户端。

```
export class DataRoute {
    public router = Router() as Router;

    constructor() {
        this.router.use((req, res: any, next) => {
            const successs = 200;
            res.sseSetup = () => {
                res.writeHead(successs, {
                    "Content-Type": "text/event-stream",
                    "Cache-Control": "no-cache",
                    "Connection": "keep-alive"
                });
                res.connection.setTimeout(0);
            };

            res.sseSend = (data: any) => {
                res.write("data: " + data +
                    "\n\n", "utf8", () => {
                        if (res.flushHeaders) {
                            res.flushHeaders();
                        }
                    });
            };
            next();
        });

        this.router.get("/endpoint", (req, res, next) => {
            const fileName = `export${moment().format("L").replace(/\//g, "-")}.csv`;
            res.setHeader("Content-disposition", `attachment; filename=${fileName}`);
            res["sseSetup"]();
            res["sseSend"]("BOS");

            data.forEach(function (element) {
                res["sseSend"](JSON.stringify(element));
            });

            this.closeStream(res);
        });
    }

    private closeStream(res: any) {
        res.sseSend("EOS");
        res.end();
    }
} 
```