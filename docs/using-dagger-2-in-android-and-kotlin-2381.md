# 在 Android 和 Kotlin 中使用 Dagger 2

> 原文：<https://dev.to/wpanas/using-dagger-2-in-android-and-kotlin-2381>

# 是什么让我做了这篇文章？

几个月前我开始编写 Android 应用程序。我经历了基础和更高级的指南。他们都没有提到依赖注入。没有它，我很难进行适当的单元测试。我决定让它发挥作用。

我环顾四周，发现[匕首 2](https://dagger.dev) 。Google 开发的为 Android 应用程序提供依赖注入的项目。可悲的是，我很难与科特林共事。

我读了许多文章，但它们只有一些零碎的必要信息。把它们结合在一起，我让它工作了。这个帖子是我小调研的总结。不会是匕首 2 基础。如果你想了解更多，请阅读官方[用户指南](https://dagger.dev/users-guide)。这些文章启发了我:

*   [在 Kotlin 项目上设置 Dagger 2.11](https://medium.com/@malinitin/setup-dagger-2-11-on-kotlin-project-2257ad84ad7c)
*   [如何通过新的 Dagger 2(带 Kotlin)使用 Android 注射器进行活动和碎片化对象](https://medium.com/@serapbercin001/how-to-use-android-injector-for-activity-and-fragment-objects-through-new-dagger-2-with-kotlin-704eb8a98c43)
*   [带匕首的新 Android 注射器 2 —第 3 部分](https://android.jlelse.eu/new-android-injector-with-dagger-2-part-3-fe3924df6a89)
*   使用 Dagger 2(和 Kotlin)在 Android 上进行依赖注入—第 1 部分

# 如何用 Kotlin 设置匕首 2？

首先给`app/build.gradle`添加依赖关系，启用`kapt`。

```
...

apply plugin: 'kotlin-kapt'

android {
    ...
}

dependencies {
    ...
    implementation 'com.google.dagger:dagger-android:2.x'
    implementation 'com.google.dagger:dagger-android-support:2.x'
    kapt 'com.google.dagger:dagger-android-processor:2.x'
    kapt 'com.google.dagger:dagger-compiler:2.x'
} 
```

# 提供模块注入

在提供依赖关系的模块中组织您的代码。活动和片段也是依赖关系。它们需要在特殊的抽象模块中，在方法上使用`@ContributesAndroidInjector`注释。

```
@Module
abstract class ActivityProvider {
    @ContributesAndroidInjector
    abstract fun mainActivity(): MainActivity
} 
```

对于其他对象，用带`@Provides`注释的方法编写模块。

```
@Module
class CoffeeModule {
    @Provides
    @Singleton
    fun coffeeService() = CoffeeService()
}

class CoffeeService {
    fun prepareCoffee() = "Here! Espresso for you"
    fun fetchCoffee() = "espresso"
} 
```

`OrderService`取决于`CoffeeService`。你不需要用匕首用`@Inject`标注来做到这一点。

```
@Module
class OrderModule {
    @Provides
    @Singleton
    fun orderService(coffeeService: CoffeeService)
     = OrderService(coffeeService)
}

class OrderService(private val coffeeService: CoffeeService) {
    fun askForOrder() = "Would you like some ${coffeeService.fetchCoffee()}?"
} 
```

# 准备您的申请

接下来添加扩展了`AndroidInjector<DaggerApplication>`的`AppComponent`。至关重要的是，其中一个模块是`AndroidSupportInjectionModule`。

```
@Singleton
@Component(modules = [AndroidSupportInjectionModule::class, ActivityProvider::class, CoffeeModule::class, OrderModule::class])
interface AppComponent : AndroidInjector<DaggerApplication> 
```

添加您自己的`DaggerApplication`实现。这将是你的 Android 应用的起点。

```
class InjectionExampleApplication : DaggerApplication() {
    override fun applicationInjector(): AndroidInjector<out DaggerApplication> {
        val appComponent = DaggerAppComponent.builder()
            .build()

        appComponent.inject(this)

        return appComponent
    }
} 
```

不要忘记在`AndroidManifest.xml`中添加属性`android:name`。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.github.wpanas.injectionexample">

    <application
        android:name=".InjectionExampleApplication"
        ...
    >
        ...
    </application>

</manifest> 
```

从现在开始，模块在`AppComponent`中注册的依赖项将可以被注入。

# 注入依赖关系

最后一步是将这些依赖注入到您的活动中。将您的`MainActivity`的父类从`AppCompatActivity`更改为`DaggerAppCompatActivity`。然后添加依赖项作为带有`@Inject`注释的`lateinit var`属性。

```
class MainActivity : DaggerAppCompatActivity() {

    @Inject
    lateinit var coffeeService: CoffeeService

    @Inject
    lateinit var orderService: OrderService

    ...
} 
```

您可以对所有活动和片段进行同样的操作。给它们的父类名加上前缀`Dagger-`。这将确保依赖项被注入。不要忘记像在`ActivityProvider`中那样将那些活动和片段添加到模块中。

# 总结

这些简单的步骤使你能够使匕首 2 与科特林。所有这些代码都是在这个[存储库](https://github.com/wpanas/InjectionExample)中捕获的。对于更复杂的例子，你也可以看看我的这个[项目](https://github.com/wpanas/StateCounter)。