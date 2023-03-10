# 使用令牌桶进行速率限制

> 原文：<https://dev.to/kendru/rate-limiting-with-a-token-bucket-2456>

最近，我正在研究在工作中的应用程序中为特定端点添加速率限制的选项。大多数端点只在内部公开，我们注意不要发出超过系统处理能力的请求。然而，在一种情况下，端点对我们的客户开放，它运行一些非常密集的数据库操作，所以我们希望限制客户端可以发出请求的速率。这个功能在几乎所有的 API 网关中都可用，在许多反向代理中也可用。在我们的例子中，应用程序更新比配置更新更容易，所以我们选择了一个简单的解决方案，可以作为 Node.js 应用程序的一部分进行部署。

进入*令牌桶*。

令牌桶是一种算法，允许*令牌*以特定的速率随时间累积。这些代币然后可以被“兑换”以执行一些动作。如果没有可用的令牌，则无法执行该操作。想象一下，我们有一个盛有一些球的桶，比如说 100 个。当桶中的球少于 100 个时，机器会以每秒 1 个球的速度自动重新装满桶，直到桶再次装满。我们可以想拿多少球就拿多少球，但是一旦桶空了，我们就必须等它再次装满，然后才能拿更多的球。

如果我们使用令牌桶来限制 API 的速率，那么它允许我们设置一个请求速率(令牌被添加到桶中的速率),并且能够在短时间内将*突发到高于该速率的*(直到我们耗尽桶的容量)。让我们先来实现一个令牌桶。

#### 初始令牌桶实现

```
class TokenBucket {

    constructor(capacity, fillPerSecond) {
        this.capacity = capacity;
        this.tokens = capacity;
        setInterval(() => this.addToken(), 1000 / fillPerSecond);
    }

    addToken() {
        if (this.tokens < this.capacity) {
            this.tokens += 1;
        }
    }

    take() {
        if (this.tokens > 0) {
            this.tokens -= 1;
            return true;
        }

        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以在 Node.js/express 应用程序中使用它来限制对特定端点的请求数量:

#### 用 TokenBucket 限速

```
const express = require('express');
const app = express();

function limitRequests(perSecond, maxBurst) {
    const bucket = new TokenBucket(maxBurst, perSecond);

    // Return an Express middleware function
    return function limitRequestsMiddleware(req, res, next) {
        if (bucket.take()) {
            next();
        } else {
            res.status(429).send('Rate limit exceeded');
        }
    }
}

app.get('/',
    limitRequests(5, 10), // Apply rate limiting middleware
    (req, res) => {
        res.send('Hello from the rate limited API');
    }
);

app.listen(3000, () => console.log('Server is running')); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`/`端点被限制为在所有客户端上每秒处理 5 个请求。如果我们希望对每个客户端进行限制，那么我们可以保留一个 IP 地址(或 API 密钥)到令牌桶的映射，在每次遇到新客户端时创建一个新的令牌桶，如下例所示:

#### IP 限速

```
function limitRequests(perSecond, maxBurst) {
    const buckets = new Map();

    // Return an Express middleware function
    return function limitRequestsMiddleware(req, res, next) {
        if (!buckets.has(req.ip)) {
            buckets.set(req.ip, new TokenBucket(maxBurst, perSecond));
        }

        const bucketForIP = buckets.get(req.ip);
        if (bucketForIP.take()) {
            next();
        } else {
            res.status(429).send('Client rate limit exceeded');
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，我们需要小心，因为大量不同的 IP 可能会在内存和定时器方面产生相当大的开销来重新填充桶。在实践中，我们可能希望在一段时间后删除令牌桶，并且我们还希望推迟添加令牌，直到它们被请求，这将消除对 JavaScript 计时器的需要。这是我们新的无计时器`TokenBucket`实现:

#### 无定时器令牌桶

```
class TokenBucket {
    constructor(capacity, fillPerSecond) {
        this.capacity = capacity;
        this.fillPerSecond = fillPerSecond;

        this.lastFilled = Math.floor(Date.now() / 1000);
        this.tokens = capacity;
    }

    take() {
        // Calculate how many tokens (if any) should have been added since the last request
        this.refill();

        if (this.tokens > 0) {
            this.tokens -= 1;
            return true;
        }

        return false;
    }

    refill() {
        const now = Math.floor(Date.now() / 1000);
        const rate = (now - this.lastFilled) / this.fillPerSecond;

        this.tokens = Math.min(this.capacity, this.tokens + Math.floor(rate * this.capacity));
        this.lastFilled = now;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个实现应该是相同的，但是它只在调用`take()`时才起作用，这在大多数情况下应该更有效。

* * *

这篇文章最初发布在 [Andrew 的博客](https://kendru.github.io/)上，名为[带令牌桶的 JavaScript 速率限制。](https://kendru.github.io/javascript/2018/12/28/rate-limiting-in-javascript-with-a-token-bucket/)