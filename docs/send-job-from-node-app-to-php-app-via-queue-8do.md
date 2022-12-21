# 通过队列将“作业”从 Node-app 发送到 PHP-app。

> 原文：<https://dev.to/sergey_telpuk/send-job-from-node-app-to-php-app-via-queue-8do>

大家好。

在这篇文章中，我想分享一个非常有趣和快速的在 PHP 端执行`jobs`的解决方案。[螺旋框架](https://github.com/spiral/app)允许我们以一种简单的方式使用`jobs`。整个做好的储存库是[这里](https://github.com/spiral/app)。

在我设计的应用程序中，`Nodejs(Nestjs)`将任务发送给`PHP`。
看 Nodejs `job` :

```
import {IJob} from "../interfaces/job.interface";

export class Ping implements IJob {
    readonly JOB = 'App.Job.Amqp.Ping'; //namespace(path) to job on PHP side

    constructor(
        private readonly value: string
    ) {

    }

} 
```

`job.interface`

```
 export interface IJob {
    JOB: string;
} 
```

这个`dto`将通过队列(RabbitMQ)发送给 PHP-app。
看`JobPusherService` :

```
import {Injectable} from "@nestjs/common";
import uuid4 from "uuid/v4";
import {IJob} from "./interfaces/job.interface";
import amqp from "amqplib";

@Injectable()
export class JobPusherService {
    readonly CONNECT = 'amqp://rabbit:rabbit@rabbitmq:5672';

    async send(job: IJob) {
        const server = await amqp.connect(this.CONNECT);
        const channel = await server.createChannel();

        const jobExecuting = job.JOB;

        delete job.JOB;

        channel.sendToQueue(
            'contrived_queue',
            Buffer.from(
                JSON.stringify(job)
            ),
            {
                headers: {
                    "rr-id": uuid4(),
                    "rr-job": jobExecuting,
                    "rr-attempt": 1000_000_000_000,
                    "rr-maxAttempts": 1000_000_000_000,
                    "rr-timeout": 1000_000_000_000,
                    "rr-delay": 1000_000_000_000,
                    "rr-retryDelay": 1000_000_000_000,
                }
            }
        );
    }
} 
```

那个用必要的头包装`dto`,并将其推入队列。

向上移动到`PHP-app`。引擎盖下，螺旋框架采用[走鹃](https://github.com/spiral/roadrunner)。所有必要的配置都在`.rr.yml` :

```
# http service configuration.
http:
  address: 0.0.0.0:8080

  http2:
    # enable HTTP/2, only with TSL, enabled by default
    enabled: true

  workers:
    command: "php  app.php"

    # max transfer channels, default 128
  maxConcurrentStreams: 128

  ssl:
    # force redirect to https connection
    redirect: true

    # custom https port (default 443)
    port:  443

    # ssl cert
    cert: ./certs/server.crt

    # ssl private key
    key: ./certs/server.key

# queue and jobs
jobs:
  amqp:
    addr: amqp://rabbit:rabbit@rabbitmq:5672/

  dispatch:
    app-job-amqp*.pipeline: amqp

  pipelines:
    amqp:
      broker: amqp
      queue:  contrived_queue

  consume: ["amqp"]

  workers:
    command: "php  app.php"
    pool.numWorkers: 10

# serve static files
static:
  dir:    "public"
  forbid: [".php", ".htaccess"]

# control the max memory usage
limit:
  services:
    http.maxMemory: 100
    jobs.maxMemory: 100

rpc:
  listen: tcp://php-app:6001 #for watcher 
```

欲了解更多详细信息，请查看本文档。

`Job`看起来像:

```
/**
 * (QueueInterface)->push(new PingJob(["value"=>"my value"]));
 */
class Ping extends AbstractJob
{
    /**
     * @param string $id
     * @param string $value
     */
    public function do(string $id, string $value)
    {

        // do something
        error_log("pong by {$id}, {$value}");
    }
} 
```

如您所见，执行`jobs`非常容易，也非常快。

待续...