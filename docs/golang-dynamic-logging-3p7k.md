# 用于微服务的 Golang 动态测井

> 原文：<https://dev.to/rusty_sys_dev/golang-dynamic-logging-3p7k>

这是我第一次写这样的帖子..如果任何人有任何评论或意见，请告诉我。

我认为动态日志应该在任何微服务环境中实现。我想我应该从动态日志开始..简单地说，应该包括以下几点。

*   维护者应该能够在执行过程中改变应用程序的日志级别。

## 样本应用

使用`go.uber.org/zap`库 [LevelEnablerFunc](https://godoc.org/go.uber.org/zap#LevelEnablerFunc) 你应该能够使用任何你想要的协议实现接口？(这个没测试过)..也就是说，对于我的示例应用程序，我将使用 HTTP 接口，因此我将使用 zap 内置的 [ServeHttp](https://godoc.org/go.uber.org/zap#AtomicLevel.ServeHTTP) 方法。

首先，我们需要定义我们的日志记录器，并构建一个 http 路由器来接受日志级别的更改。这是通过利用内置在`zap.AtomicLevel`对象中的上述`ServeHttp`函数来完成的。

#### 设置记录器

```
config := zap.NewProductionEncoderConfig()
...
encoder := zapcore.NewJSONEncoder(config)
atom := zap.NewAtomicLevel()
    logr := zap.New(zapcore.NewCore(encoder, zapcore.Lock(os.Stdout), atom)) 
```

#### 动态日志级别界面

```
mux := http.NewServeMux()
mux.Handle("/log_level", atom)
go http.ListenAndServe(":1065", mux) 
```

我正在运行一个简单的循环，重复各种日志消息，让我们看到预期的输出。在构建和运行应用程序之后，我们可以通过 curl 与它进行交互。

#### 改变记录级别

```
curl -X PUT -d '{"level":"debug"}' localhost:1065/log_level
curl -X PUT -d '{"level":"info"}' localhost:1065/log_level
curl -X PUT -d '{"level":"info"}' localhost:1065/  # EXPECTED 404
curl -X GET localhost:1065/log_level 
```

[![APP_GIF](img/f37b76a8156f29aa9692d861c08b9079.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k_478ucd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://imgur.com/mSc5ET9.gif)

## 示例应用代码

```
package main

import (
    "net/http"
    "os"
    "time"

    "go.uber.org/zap"
    "go.uber.org/zap/zapcore"
)

func main() {
    undo := initLogger()
    defer undo()

    for {
        zap.S().Info("test_logs")
        zap.S().Debug("test_logs_debug")
        time.Sleep(1000 * time.Millisecond)
    }
}

func initLogger() func() {
    config := zap.NewProductionEncoderConfig()
    config.EncodeTime = func(t time.Time, enc zapcore.PrimitiveArrayEncoder) {
        nanos := t.UnixNano()
        millis := nanos / int64(time.Millisecond)
        enc.AppendInt64(millis)
    }
    encoder := zapcore.NewJSONEncoder(config)
    atom := zap.NewAtomicLevel()
    logr := zap.New(zapcore.NewCore(encoder, zapcore.Lock(os.Stdout), atom))

    mux := http.NewServeMux()
    mux.Handle("/log_level", atom)
    go http.ListenAndServe(":1065", mux)

    return zap.ReplaceGlobals(logr)
} 
```