# Hystrix 的@CacheResult 不支持 RxJava

> 原文：<https://dev.to/rinky_05/cacheresult-of-hystrix-is-not-working-with-rxjava-1enk>

我使用 Hystrix 请求级缓存，但使用 RxJava 时，会根据调度程序的类型创建新线程。我正在使用 schedulers.io.
我得到错误 Hystrix 请求上下文应该被初始化，而它已经被初始化了，但是它们是 threadlocal，因此只是主线程。
可以一起用吗？