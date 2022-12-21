# 第七天:嘿，看，我有一份大象的名单

> 原文：<https://dev.to/rahulchowdhury/day-7-hey-look-i-got-a-list-of-elephants-46gk>

嗨，今天是我求职旅程的第七天。

# 昨天的工作

实现 Jetpack 的导航组件轻而易举。很高兴看到 Android 应用程序开发已经走了多远。

Android 核心团队在使常见用例易于实现方面做得非常出色，这样开发人员就可以更多地关注他们应用程序的业务逻辑。

除此之外，我还做了一些小的重构。

# 今天？

我开始从[大象 API](https://elephant-api.herokuapp.com/) 中获取大象列表。

在缓存大象列表时，我有两个选择:

*   **解决方案 A** :在本地数据库中序列化并存储一个大象列表，与之前的`Elephant`模型分开。
*   **解决方案 B** :将列表中的所有大象对象存储为单独的记录，就像它们存储在大象档案屏幕中一样。

这两种方法各有利弊，但最终我决定采用方案 b。

### 利弊

*   **溶液 A** :
    *   *优点*:很容易实现并检查最后更新时间，以确定是否需要重新获取列表。
    *   *缺点*:不灵活。必须在两个地方维护相似的数据。会导致数据不一致。例如，假设主屏幕中的大象列表被刷新，DB 中的列表数据被更新为最新的副本。然而，配置文件屏幕仍然从单个大象实体获取缓存数据，这些实体可能没有最新的副本，因为它们的缓存时间还没有到期。
*   **溶液 B** :
    *   *优点*:数据是一致的，因为列表屏幕和配置文件屏幕上的大象细节都是从相同的数据库行获取的。因此，如果一个屏幕更新了数据，另一个屏幕会自动获得最新的更新。
    *   *缺点*:缓存逻辑不像解决方案 A 那样简单，因为需要不止一项来检查数据的陈旧性。

这是我用来确定列表陈旧性的逻辑:

```
private suspend fun refreshElephantListIfNeeded() {
    val staleTime = System.currentTimeMillis() - Constants.Time.FRESHNESS_PERIOD_IN_MILLIS
    val staleElephants = elephantDao.loadStaleElephants(staleTime)

    if (staleElephants.isNotEmpty()) {
        val elephantListResponse = elephantApiService.fetchElephants()
        val normalisedElephants = elephantListResponse
            .filter { it.name != null }
            .map { it.toElephant() }

        elephantDao.saveMultiple(normalisedElephants)
    }
} 
```

你可以在这里找到更新的项目:[https://github.com/rahulchowdhury/elly](https://github.com/rahulchowdhury/elly)

我也在探索我的应用程序测试库的选项。让我们看看我发现了什么。