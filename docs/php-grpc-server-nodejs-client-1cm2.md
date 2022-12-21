# PHP gRPC-服务器和 NodeJS-客户端

> 原文：<https://dev.to/sergey_telpuk/php-grpc-server-nodejs-client-1cm2>

你好朋友，希望你一切都好！

我将描述运行 PHP gRPC-server 和 code NodeJS gRPC- client 有多容易。在本文中，我将只强调设置工作流的更重要的部分。[这里](https://github.com/sergey-telpuk/php-grpc-server-nodejs-client)，你可以找到一个做过的资源库。如果你不熟悉 gRPC，看看下面的[链接](https://grpc.io/)。

会有考虑快速解决方案 [spiral/php-grpc](https://github.com/spiral/php-grpc) 到 gRPC-server。让我们开始吧。

首先看 [docker-compose](https://github.com/sergey-telpuk/php-grpc-server-nodejs-client/blob/master/docker-compose.yml) 文件:

```
version: '3.3'

services:

    grpc_php_server: # grpc server
        build:
            context: ./containers/php
            dockerfile: php.docker
        container_name: grpc_php_server
        working_dir: /app
        command: ./rr-grpc serve -v -d
        volumes:
            - ./src/php:/app

    grpc_php_protoc: # protoc generator 
        image:  grpc/php
        container_name: grpc_protoc
        working_dir: /app
        volumes:
            - ./src/php:/app

    client_nodejs: # grpc client
        image: node:latest
        container_name: client_nodejs
        command: node ./client.js
        working_dir: /app
        links:
          - grpc_php_server
        depends_on:
          - grpc_php_server
        volumes:
            - ./src/nodejs:/app 
```

如您所见，这是一个非常简单的`docker-compose`文件。
*注意:如果你想到处玩，别忘了抢所有的依赖。*

```
docker-compose run grpc_php_server composer install docker-compose run client_nodejs npm install 
```

我尽可能用最原始的方式起草了这个文件😏。
`contrived.proto`代表`server`和`client` :

```
syntax = "proto3";

package service;

service ContrivedService {
    rpc ContrivedMethod (ContrivedMessageRequest) returns (ContrivedMessageResponse) {
    }
}

message ContrivedMessageRequest {
    string body = 1;
}

message ContrivedMessageResponse {
    string body = 1;
} 
```

那个被放在`php/proto`和`nodejs/proto`目录下。

为了生成 gRPC-server 所需的接口和启动框架，使用了以下命令:

```
docker-compose run grpc_php_protoc protoc --plugin=./protoc-gen-php-grpc --php_out=/app --php-grpc_out=/app ./proto/contrived.proto 
```

`grpc-server`的设置托管在根目录`.rr.yaml` :

```
grpc:
  listen: "tcp://:3000"
  proto: "./proto/contrived.proto"
  workers:
    command: "php  worker.php"
    pool:
      numWorkers: 1
      maxJobs:    1 
```

`worker.php`可以是这样的:

```
<?php
declare(strict_types=1);
/**
 * Sample GRPC PHP server.
 */
use Spiral\Goridge;
use Spiral\RoadRunner;
ini_set('display_errors', 'stderr');
require "vendor/autoload.php";
$server = new \Spiral\GRPC\Server();
$server->registerService(\Service\ContrivedServiceInterface::class, new \Service\ContrivedService());
$w = new RoadRunner\Worker(new Goridge\StreamRelay(STDIN, STDOUT));
$server->serve($w); 
```

`ContrivedService.php`可以是这样的:

```
<?php
namespace Service;
use Service\ContrivedMessageRequest;
use Service\ContrivedMessageResponse;
use Service\ContrivedServiceInterface;
use Spiral\GRPC;
class ContrivedService implements ContrivedServiceInterface
{
    /**
     * @param GRPC\ContextInterface $ctx
     * @param ContrivedMessageRequest $in
     * @return ContrivedMessageResponse
     *
     * @throws GRPC\Exception\InvokeException
     */
    public function ContrivedMethod(GRPC\ContextInterface $ctx, ContrivedMessageRequest $in): ContrivedMessageResponse
    {
        $response = new ContrivedMessageResponse();
        $response->setBody("Hello");
        return $response;
    }
} 
```

那么，让我们来看看 js-client。

`client.js`可以是这样的:

```
const path = require('path');
const PROTO_PATH = path.resolve(__dirname, './proto/contrived.proto');
const GRPCClient = require('node-grpc-client');

const myClient = new GRPCClient(PROTO_PATH, 'service', 'ContrivedService', 'grpc_php_server:3000');

const dataToSend = {
    body: 'Nodejs client!'
};

myClient.runService('ContrivedMethod', dataToSend, (err, res) => {
    if (err) {
        console.error(err);
    }

    console.log('Service response\n', res);
}); 
```

为了进行试验，使用了以下命令:

```
 docker-compose up 
```

输出结果:

```
grpc_php_server    | DEBU[0000] [rpc]: started                               
grpc_php_server    | DEBU[0000] [grpc]: started                              
client_nodejs      | Service response
client_nodejs      |  { body: 'Hello' } 
```

如您所见，开始使用 gRPC 并开始考虑将它应用到我们的工作流程中非常简单。

采用 GRPC 有什么好处:

1.  很好理解。
2.  Web 基础设施已经建立在 HTTP 之上。
3.  用于测试、检查和修改的强大工具。
4.  客户机/服务器之间的松散耦合使得更改变得容易。
5.  每种语言的高质量 HTTP 实现。