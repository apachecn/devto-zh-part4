# 分布式锁定和速率限制

> 原文：<https://dev.to/da440dil/distributed-locking-rate-limiting-bd>

在我之前的一个项目中，我遇到了两个问题。而且这并不是[缓存失效和命名东西](https://martinfowler.com/bliki/TwoHardThings.html)可能你想的那样:)。这就是分布式锁定和分布式速率限制。在我看来，我很好地解决了这两个问题。现在，经过一些简化和改造后，我认为解决方案已经准备好发布，它们是:[Go-locker](https://github.com/da440dil/go-locker)&[Go-counter](https://github.com/da440dil/go-counter)on[Go](https://golang.org/)和[js-locker](https://github.com/da440dil/js-locker)&[js-counter](https://github.com/da440dil/js-counter)on[TypeScript](http://www.typescriptlang.org/)。欢迎评论。