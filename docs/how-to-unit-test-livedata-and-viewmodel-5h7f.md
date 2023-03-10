# 如何对 LiveData 和 ViewModel 进行单元测试

> 原文：<https://dev.to/arthlimchiu/how-to-unit-test-livedata-and-viewmodel-5h7f>

*原载 [**我的博客**](https://www.arthlimchiu.com/2019/07/03/how-to-unit-test-live-data-and-view-model.html)* 。

[源代码](https://github.com/arthlimchiu/unit-test-live-data)

### 导入依赖关系

```
implementation 'androidx.lifecycle:lifecycle-extensions:2.0.0'
testImplementation 'androidx.arch.core:core-testing:2.0.1'
testImplementation 'org.mockito:mockito-core:2.28.2' 
```

Enter fullscreen mode Exit fullscreen mode

*查看[官方安卓文档](https://developer.android.com/jetpack/androidx/versions)获取这些安卓包的最新版本。*

对于 Mockito，查看他们的 Github 库的最新版本。

### 创建简单的用户类

```
data class User(val id: Int) 
```

Enter fullscreen mode Exit fullscreen mode

### 创建视图模型

```
class MainViewModel : ViewModel() {

    private val _user = MutableLiveData<User>()

    val user: LiveData<User>
        get() = _user

    fun fetchUser(id: Int) {
        val user = User(id)

        _user.value = user
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个助手函数来模仿类的类型(泛型)

在您的测试文件夹中创建一个 kotlin 文件——mockitotils . kt。

```
inline fun <reified T> mock(): T = Mockito.mock(T::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

### 创建单元测试

```
class MainViewModelTest {

    @get:Rule
    val rule = InstantTaskExecutorRule()

    private lateinit var viewModel: MainViewModel

    private val observer: Observer<User> = mock()

    @Before
    fun before() {
        viewModel = MainViewModel()
        viewModel.user.observeForever(observer)
    }

    @Test
    fun fetchUser_ShouldReturnUser() {
        val expectedUser = User(1)

        viewModel.fetchUser(expectedUser.id)

        val captor = ArgumentCaptor.forClass(User::class.java)
        captor.run {
            verify(observer, times(1)).onChanged(capture())
            assertEquals(expectedUser, value)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
@get:Rule
val rule = InstantTaskExecutorRule() 
```

Enter fullscreen mode Exit fullscreen mode

这条规则基本上允许我们同步运行 LiveData。这条规则来自前面导入的核心测试包。

```
private val observer: Observer<User> = mock() 
```

Enter fullscreen mode Exit fullscreen mode

我们制作的辅助函数将帮助我们模拟我们的观察者。试着用标准的方式嘲笑它，你就会明白我的意思。

```
val captor = ArgumentCaptor.forClass(User::class.java)
captor.run {
    verify(observer, times(1)).onChanged(capture())
    assertEquals(expectedUser, value)
} 
```

Enter fullscreen mode Exit fullscreen mode

**ArgumentCaptor** 做类名所称的事情——捕获参数。当我们的观察者的方法被调用时，我们就开始争论。

添加`verify(observer, times(1)).onChanged(capture())`允许您捕获 LiveData 被多次调用的实例，而您只希望它被调用一次。

断言我们已经创建的 **expectedUser** 等于我们的 LiveData 发出的用户。

最后，进行该死的测试。