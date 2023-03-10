# 在应用程序代码和相应的测试之间切换 RxJava 调度程序

> 原文：<https://dev.to/varunbarad/switching-rxjava-schedulers-between-app-code-and-corresponding-tests-2coo>

在 app 代码中使用合适的调度器和在测试中使用 trampoline 调度器之间切换的简单方法。

当使用 RxJava 时，我们需要指定我们想要用来 1)执行给定任务和 2)返回操作链结果的调度程序。

我在为一家公司做带回家的挑战时发现了一个有趣的方法，如下:

```
object VarunSchedulers {
    private var schedulers: RxSchedulers = RxSchedulers.Default

    fun enableTesting() {
        this.schedulers = RxSchedulers.Test
    }

    fun disableTesting() {
        this.schedulers = RxSchedulers.Default
    }

    fun io(): Scheduler = this.schedulers.io()
    fun computation(): Scheduler = this.schedulers.computation()
    fun main(): Scheduler = this.schedulers.main()

    private sealed class RxSchedulers {
        abstract fun io(): Scheduler
        abstract fun computation(): Scheduler
        abstract fun main(): Scheduler

        object Test : RxSchedulers() {
            override fun io(): Scheduler = Schedulers.trampoline()
            override fun computation(): Scheduler = Schedulers.trampoline()
            override fun main(): Scheduler = Schedulers.trampoline()
        }

        object Default : RxSchedulers() {
            override fun io(): Scheduler = Schedulers.io()
            override fun computation(): Scheduler = Schedulers.computation()
            override fun main(): Scheduler = AndroidSchedulers.mainThread()
        }
    }
} 
```

现在让我们以获取网络查询为例，我们可以简单地这样做

```
pandaService.getCutestPanda()
    .subscribeOn(VarunSchedulers.io())
    .observeOn(VarunSchedulers.main())
    ... 
```

当我们编写单元测试时，我们可以使用`@Before`和`@After`方法轻松地打开和关闭测试模式。

```
class SomeTestClass {
    @Before
    fun setupTests() {
        VarunSchedulers.enableTesting()
    }

    @After
    fun tearDown() {
        VarunSchedulers.disableTesting()
    }

    // Write your tests
} 
```

现在，只要我们在测试中，就会自动分配`trampoline`调度器，如果我们不在测试中，默认情况下会分配相应的调度器。

如果你有更多这样的锦囊妙计或你不清楚的东西，或者你只是想聊天，那么在 twitter 上打电话给我。