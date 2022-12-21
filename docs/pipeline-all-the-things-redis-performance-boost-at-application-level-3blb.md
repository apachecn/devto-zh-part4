# 管道化所有东西:Redis 在应用程序级别的性能提升

> 原文：<https://dev.to/erikaheidi/pipeline-all-the-things-redis-performance-boost-at-application-level-3blb>

当我在[数字海洋](https://dev.to/digitalocean)为我的[最新教程](https://www.digitalocean.com/community/tutorials/how-to-perform-redis-benchmark-tests)做研究时，我有机会使用 Redis，我已经在十几台服务器上运行了各种基准测试。

令我颇感惊讶的是，如果你只是用所谓的**流水线技术**将*一些命令打包在一起，你的 Redis 服务器每秒可以多处理多少请求。*

这是因为即使 Redis 非常快，请求也会消耗系统的其他资源。这就像买几片单独的面包片，每片都包在一个包里，而不是得到一整条面包。

使用`redis-benchmark`工具的常规基准测试应该是这样的:

```
redis-benchmark -t get,set -q 
```

Enter fullscreen mode Exit fullscreen mode

```
SET: 95785.44 requests per second
GET: 97370.98 requests per second 
```

Enter fullscreen mode Exit fullscreen mode

现在这将模拟 16 个命令的流水线:

```
redis-benchmark -t get,set -q -P 16 
```

Enter fullscreen mode Exit fullscreen mode

```
SET: 877193.00 requests per second
GET: 1351351.38 requests per second 
```

Enter fullscreen mode Exit fullscreen mode

每秒从**97370.90**到**1351351.38**个请求！！！

[![very fast](img/3977ed248ab9fed4e1a6316e5cb140e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZuzJZwi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltwvfpxs10ml02f4gqon.gif)

这是一个需要在代码级别进行的优化，这一点我们很清楚。你可以在 Redis 的官方文档中找到更多关于 Redis 管道的信息。为了让您了解代码的样子，这里有一段摘自该文档的实际 Ruby 代码，它在使用和不使用流水线的情况下做了一个小的基准测试:

```
require 'rubygems'
require 'redis'

def bench(descr)
    start = Time.now
    yield
    puts "#{descr}  #{Time.now-start} seconds"
end

def without_pipelining
    r = Redis.new
    10000.times {
        r.ping
    }
end

def with_pipelining
    r = Redis.new
    r.pipelined {
        10000.times {
            r.ping
        }
    }
end

bench("without pipelining") {
    without_pipelining
}
bench("with pipelining") {
    with_pipelining
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是输出(也是从他们的文档中复制的):

```
without pipelining 1.185238 seconds
with pipelining 0.250783 seconds 
```

Enter fullscreen mode Exit fullscreen mode

您也可以通过 [PHP Redis 客户端](https://github.com/cheprasov/php-redis-client)库在 PHP 中利用这个特性。

查看 [Redis 客户端](https://redis.io/clients)页面，了解支持其他语言管道的客户端。

干杯！