# Go 中基于 IP 地址的速率限制 HTTP 请求

> 原文：<https://dev.to/plutov/rate-limiting-http-requests-in-go-based-on-ip-address-542g>

如果您正在运行 HTTP server，并且想要对端点的请求进行速率限制，那么您可以使用维护良好的工具，例如[github.com/didip/tollbooth](https://github.com/didip/tollbooth)。但是如果你正在构建一个非常简单的东西，你自己实现它并不困难。

已经有一个实验性的 Go 包`x/time/rate`，我们可以使用。

在本教程中，我们将创建一个简单的基于用户 IP 地址的速率限制中间件。

### 纯 HTTP 服务器

让我们从构建一个简单的 HTTP 服务器开始，它具有非常简单的端点。这可能是一个沉重的终点，这就是为什么我们要在那里增加一个速度限制。

```
package main

import (
    "log"
    "net/http"
)

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", okHandler)

    if err := http.ListenAndServe(":8888", mux); err != nil {
        log.Fatalf("unable to start server: %s", err.Error())
    }
}

func okHandler(w http.ResponseWriter, r *http.Request) {
    // Some very expensive database call
    w.Write([]byte("alles gut"))
} 
```

在`main.go`中，我们在`:8888`启动服务器，并拥有一个端点`/`。

### golang.org/x/time/rate

我们将使用`x/time/rate` Go 包，它提供了一个令牌桶限速算法。[速率限制器](https://godoc.org/golang.org/x/time/rate#Limiter)控制事件发生的频率。它实现了一个大小为`b`的“令牌桶”，最初是满的，以每秒`r`个令牌的速率重新装满。非正式地，在任何足够大的时间间隔内，限制器将速率限制为每秒 r 个令牌，最大突发大小为 b 个事件。

由于我们想实现每个 IP 地址的速率限制器，我们还需要维护一个限制器的地图。

```
package main

import (
    "sync"

    "golang.org/x/time/rate"
)

// IPRateLimiter .
type IPRateLimiter struct {
    ips map[string]*rate.Limiter
    mu  *sync.RWMutex
    r   rate.Limit
    b   int
}

// NewIPRateLimiter .
func NewIPRateLimiter(r rate.Limit, b int) *IPRateLimiter {
    i := &IPRateLimiter{
        ips: make(map[string]*rate.Limiter),
        mu:  &sync.RWMutex{},
        r:   r,
        b:   b,
    }

    return i
}

// AddIP creates a new rate limiter and adds it to the ips map,
// using the IP address as the key
func (i *IPRateLimiter) AddIP(ip string) *rate.Limiter {
    i.mu.Lock()
    defer i.mu.Unlock()

    limiter := rate.NewLimiter(i.r, i.b)

    i.ips[ip] = limiter

    return limiter
}

// GetLimiter returns the rate limiter for the provided IP address if it exists.
// Otherwise calls AddIP to add IP address to the map
func (i *IPRateLimiter) GetLimiter(ip string) *rate.Limiter {
    i.mu.Lock()
    limiter, exists := i.ips[ip]

    if !exists {
        i.mu.Unlock()
        return i.AddIP(ip)
    }

    i.mu.Unlock()

    return limiter
} 
```

`NewIPRateLimiter`创建 IP limiter 的一个实例，HTTP 服务器必须调用这个实例的`GetLimiter`来获取指定 IP 的 limiter(从映射中或生成一个新的)。

### 中间件

让我们升级我们的 HTTP 服务器，并为所有端点添加中间件，这样，如果 IP 达到限制，它将响应 429 过多的请求，否则，它将继续处理请求。

在`limitMiddleware`函数中，每当中间件接收到一个 HTTP 请求时，我们调用全局限制器的`Allow()`方法。如果桶中没有剩余的令牌,`Allow()`将返回 false，我们向用户发送 429 太多请求响应。否则，调用`Allow()`将消耗桶中的一个令牌，我们将控制权传递给链中的下一个处理程序。

```
package main

import (
    "log"
    "net/http"
)

var limiter = NewIPRateLimiter(1, 5)

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", okHandler)

    if err := http.ListenAndServe(":8888", limitMiddleware(mux)); err != nil {
        log.Fatalf("unable to start server: %s", err.Error())
    }
}

func limitMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        limiter := limiter.GetLimiter(r.RemoteAddr)
        if !limiter.Allow() {
            http.Error(w, http.StatusText(http.StatusTooManyRequests), http.StatusTooManyRequests)
            return
        }

        next.ServeHTTP(w, r)
    })
}

func okHandler(w http.ResponseWriter, r *http.Request) {
    // Some very expensive database call
    w.Write([]byte("alles gut"))
} 
```

### 建造&运行

```
go get golang.org/x/time/rate
go build -o server .
./server 
```

### 测试

有一个非常好的工具我喜欢用来进行 HTTP 负载测试，叫做[begeta](https://github.com/tsenart/vegeta)(也是用 Go 编写的)。

```
brew install vegeta 
```

我们需要创建一个简单的配置文件，说明我们想要产生什么请求。

```
GET http://localhost:8888/ 
```

然后运行攻击 10 秒钟，每个时间单位 100 个请求。

```
vegeta attack -duration=10s -rate=100 -targets=vegeta.conf | vegeta report 
```

结果你会看到一些请求返回 200，但是大多数返回 429。