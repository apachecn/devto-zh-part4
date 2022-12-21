# 如何使用并行协同程序和改进获得黑客新闻头条

> 原文：<https://dev.to/onmyway133/how-to-get-hacker-news-top-stories-using-parallel-coroutine-and-retrofit-23pn>

```
interface Api {
    @GET("topstories.json?print=pretty")
    suspend fun getTopStories(): List<Int>

    @GET("item/{id}.json?print=pretty")
    suspend fun getStory(@Path("id") id: Int): Item
} 
```

```
class Repo {
    fun api(): Api {
        return Retrofit.Builder()
            .baseUrl("https://hacker-news.firebaseio.com/v0/")
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
            .create(Api::class.java)
    }
} 
```

```
class ViewModel(val repo: Repo): ViewModel() {
    val items = MutableLiveData<ArrayList<Item>>()

    suspend fun load() {
        try {
            val ids = repo.api()
                .getTopStories()
                .take(20)

            val items = ids.map {
                repo.api().getStory(it)
            }
            this.items.value = items.toCollection(ArrayList())
        } catch (e: Exception) {
            this.items.value = arrayListOf()
        }
    }
} 
```

## 并行运行

以上连续运行。要并行运行，我们可以使用`async`

```
import kotlinx.coroutines.async
import kotlinx.coroutines.coroutineScope

class ViewModel(val repo: Repo): ViewModel() {
    val items = MutableLiveData<ArrayList<Item>>()

    suspend fun load() {
        try {
            val ids = repo.api()
                .getTopStories()
                .take(20)

            coroutineScope {
                val items = ids
                    .map { async { repo.api().getStory(it) } }
                    .awaitAll()

                this@ViewModel.items.value = items.toCollection(ArrayList())
            }

        } catch (e: Exception) {
            this.items.value = arrayListOf()
        }
    }
} 
```

## 平行分解

[https://medium . com/@ Eliza ROV/structured-concurrency-722d 765 aa 952](https://medium.com/@elizarov/structured-concurrency-722d765aa952)

> 有了结构化并发，异步协程生成器就像 launch 一样成为了协程 Scope 的扩展。你不能再简单地写 async { … }，你必须提供一个作用域。并行分解的一个典型例子是:

## 验光仪

[https://proandroiddev . com/part-2-coroutine-cancellation-and-structured-concurrency-2d BC 6583 c 07d](https://proandroiddev.com/part-2-coroutine-cancellation-and-structured-concurrency-2dbc6583c07d)

> 协程作用域函数可用于创建一个自定义作用域，该作用域挂起并仅在该作用域内启动的所有协程完成时完成。如果协程范围内的任何一个子协程抛出异常，所有其他正在运行的兄弟协程都被取消，并且该异常沿层次向上传播。如果层次结构顶部的父协同程序不处理这个错误，它也将被取消。

## awaitAll

[https://kot Lin . github . io/kot linx . corroutes/kot linx-核心/kot linx . corroutes/await-all . html](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/await-all.html)

> 在不阻塞线程的情况下等待给定延迟值的完成，并在所有延迟计算完成时以值列表正常恢复，或者在任何计算异常完成(包括取消)时以第一个抛出的异常恢复。
> 
> 此函数不等同于 deferreds.map { it.await() }，后者仅在顺序等待失败的 deferred 时才会失败，而此 awaitAll 在任何一个 deferred 失败时都会立即失败。
> 
> 这种暂停功能是可以取消的。如果当前协程的作业在该挂起函数等待时被取消或完成，则该函数会立即恢复，并出现 CancellationException。

## 阅读更多

*   [https://jivimberg . io/blog/2018/05/04/parallel-map-in-kot Lin/](https://jivimberg.io/blog/2018/05/04/parallel-map-in-kotlin/)
*   [https://stack overflow . com/questions/53535977/coroutines-run blocking-vs-coroutinescope](https://stackoverflow.com/questions/53535977/coroutines-runblocking-vs-coroutinescope)
*   awaital[https://kot Lin . github . io/kot linx . corroutes/kot linx-corroutes-core/kot linx . corroutes/await-all . html](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/await-all.html)