# 使用 dotnet core 为博客构建一个 TDD Restful WebAPI

> 原文：<https://dev.to/mrsimi/build-a-tdd-restful-webapi-for-a-blog-using-dotnet-core-5fo8>

# 简介

在这篇文章中，我们将讨论在博客的 WebAPI 中实现 TDD(测试驱动开发),兴奋吗？但是在我们深入研究所有的编码之前，我们将稍微讨论一下 TDD。

## 那么什么是测试驱动开发呢？

我实际上喜欢维基百科的定义，似乎用几句话就能给出全貌。根据撰写该部分的人所说，TDD 是一个软件开发过程，它依赖于非常短的开发周期的*重复；*需求*变成*非常具体的测试用例*；然后*软件*改进为*只通过新的测试*。*

但是老实说，上面的定义就是 TDD 的含义。是的，这里涉及到很多软件术语，我们很快就会讲到，但是用简单的英语理解这些术语会让我们走上写作的道路。

### TDD 涉及的步骤有哪些？

红绿重构周期是软件术语，它粉饰了 TDD 中涉及的所有步骤。

1.  RED:这是第一步，你写代码来测试你的用例。请记住，您正在启动一个空白代码文件，因此这个测试肯定会失败。不要为了通过测试而写任何东西，甚至是定义类。这一失败就是它被称为**红色**的原因。
2.  GREEN:这是下一步，您要编写代码以通过测试。此时的规则是编写足够的代码来通过测试。继续写代码，直到测试通过，不要让一个测试保持红色，然后再写一个。
3.  重构:这是你通过重构让你的代码变得漂亮的一步。你可以通过做一些事情让它变得更干净，这些事情不仅限于遵守你的语言、你的设计、你的建筑等等。

这里是总结，为你的用例写测试，第一次必须失败，然后写足够的产品代码使它通过。

## 那么让我们来编码吧

### 创建一个 WebAPI 项目

在本教程中，我将使用 Visual Studio Community 2017，但可以随意使用任何支持 dotnet core 的 IDE。如果你像我一样，请遵循以下步骤:

1.  文件>新建项目>选择 ASP.NET 核心 Web 应用程序
2.  重命名为您选择的名称，我的是“TddBlogDevto”，然后**点击确定**
3.  它会把你带到选择模板的页面，只需**选择 API** 和**点击 OK**
4.  你应该开始运行，运行它以确保一切正常。

### 测试设置

我们将在本教程中做的这种测试被称为**单元测试**，一瘸一拐地；当你测试可以被描述为函数/方法的单元时。

测试大部分是使用某种框架来完成的，以使工作更容易。在这种情况下，我们将使用 **xunit 框架**。以下是设置它的一些事项:

1.  右键单击您的解决方案>添加>新建项目
2.  在项目面板**上选择 xUnit 测试项目(。NET Core)模板**
3.  重命名为一些真正有意义并且与你的项目相关的东西，在我的例子中就是 **TddBlogDevto。测试**
4.  你应该有一个新的项目和一个默认的类，我们应该把它的名字改成一个有意义的名字。有意义的命名在所有类型的编码中都是必不可少的。

### 编写我们的第一个测试

正如标题所示，我们正在为博客编写 WebAPI。所以很明显，我们希望任何使用我们 API 的人得到的第一件事是帖子列表，对吗？

这就是你的第一个测试用例，编写一个显示文章列表的测试。好吗？

在这一点上有点行话。你知道，我们说过要显示一个列表，但是我们如何欺骗这个类，让它相信我们有一个列表，而实际上却没有。这样做的方法叫做创建 *mock* ，有点像为真实类创建一个假值。

在这种情况下，我们将尝试模拟一个 post 列表，我们将使用一个名为 **Moq** 的 nuget 包来完成。我知道没错，很多框架和库只是为了编写测试，但是你知道*所有的程序员都很懒*，所以我们寻找让我们以简洁的方式编写更少代码的方法。

*右键点击你的解决方案>管理 NuGet 包>浏览>搜索“MOQ”>安装*

我们现在有了一个模仿类的方法。耶！

在我们走远之前，

*右键单击您的测试项目(BlogDevto。测试)>添加>参考>选择你的生产项目(BlogDevto)。*

这有助于添加对生产项目的引用。

将这段代码写在测试项目的类中，

```
public class BlogService
    {
        private readonly BlogController _blogController;
        private Mock<List<Post>> _mockPostsList;

        public BlogService()
        {
            _mockPostsList = new Mock<List<Post>>();
            _blogController = new BlogController(_mockPostsList.Object);
        }

        [Fact]
        public void GetTest_ReturnsListofPosts()
        {
            //arrange
            var mockPosts = new List<Post> {
                new Post{Title = "Tdd One"},
                new Post{Title = "Tdd and Bdd"}
            };

            _mockPostsList.Object.AddRange(mockPosts);

            //act
            var result = _blogController.Get();

            //assert
            var model = Assert.IsAssignableFrom<ActionResult<List<Post>>>(result);
            //Assert.Equal(2, model.Value.Count);
        } 
```

Enter fullscreen mode Exit fullscreen mode

### 我们来解释一下测试代码

```
private readonly BlogController _blogController;
private Mock<List<Post>> _mockPostsList; 
```

Enter fullscreen mode Exit fullscreen mode

**BlogController** 来自我们的生产代码。控制器是我们的 webAPI 的入口点，它是我们希望在产品代码中实现的一个类，以公开我们的 WebAPI。**模拟**是**岗位列表**的模拟类。我们有一个想要模仿的帖子列表，所以我们使用这个代码(我们之前讨论过模仿)。

```
public BlogService()
        {
            _mockPostsList = new Mock<List<Post>>();
            _blogController = new BlogController(_mockPostsList.Object);
        } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个构造函数(一个与类同名的方法),我们在这里为之前声明的变量赋值。我们基本上是说，当创建这个类时，在传入 **_mockPostsList 时，将这些值分配给一个 **Mock > ()** 的实例和一个 **BlogController** 的实例。对象**

我们刚刚讨论的前七行代码只是一种面向对象的设计思维方式，它将一个对象注入到构造函数中。请耐心等待，不要偏离本教程的目标，TDD。

```
 [Fact]
        public void GetTest_ReturnsListofPosts()
        {
            //..
         } 
```

Enter fullscreen mode Exit fullscreen mode

这是测试一个帖子列表是否被返回的方法，从名字就可以看出来。名字在编码中非常重要，尤其是在测试中；你的方法名应该解释测试做了什么。

设计这个类的**【事实】**属性来自我们用来测试的库， **xunit** 。这是告诉编译器这个类方法是测试方法的一种方式。

我们在 dotnet 中对待测试的方式，我认为大多数语言都是 3As 安排，行动，断言。我会边走边解释。

```
 //arrange
            var mockPosts = new List<Post> {
                new Post{Title = "Tdd One"},
                new Post{Title = "Tdd and Bdd"}
            };

            _mockPostsList.Object.AddRange(mockPosts); 
```

Enter fullscreen mode Exit fullscreen mode

这是编曲部分。我们正在安排我们需要的数据或输出，在这种情况下是 post 列表。变量 mockPosts 只是一个普通老式 csharp 变量，代表一个帖子列表。在创建了**模仿帖**之后，我们将其添加到了在*模仿帖列表*之前创建的列表中。

这是嘲弄的本质。我们有一个*帖子*的类，并试图创建一些假/模拟帖子，我们稍后将它们添加到模拟/假列表中作为我们的参考。

```
//act
var result = _blogController.Get(); 
```

Enter fullscreen mode Exit fullscreen mode

表演部分。这里我们得到了用例的假定实现(即一个列出所有帖子的方法)。我们建议博客控制器类应该有一个 **Get** 类。

```
//assert
var model = Assert.IsAssignableFrom<ActionResult<List<Post>>>(result); 
```

Enter fullscreen mode Exit fullscreen mode

断言部分是我们检查来自 act 的建议结果是否与引用相关的部分。

在我们的例子中，我们断言来自 act 的**结果**实际上是帖子的**列表。**

要运行:

*打开测试浏览器:测试>窗口>测试浏览器*

这将显示该类中的所有方法。

运行**GetTest _ ReturnsListofPosts**测试。记住它一定会失败。如果没有，你就失败了。

*还要记住，编译器错误也是一种失败*

### 如果测试失败了，我们就开始考虑如何通过。

*   我们需要一个类 **Post** ，因为我们要返回 Post 列表。因此，进入生产项目(TddBlogDevto)并添加一个类文件，并将其命名为 *Post* 。把下面的代码放进去。

```
public class Post
{ 
   public Guid Id { get; set; }
   public string Title { get; set; }
   public string Content { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个模型只是包含了岗位的定义；一篇帖子必须包含一个 **Id** ，一个**标题**和一个**内容**。普通的旧 Clr 对象。

*   创建一个控制器标题 **BlogController** 。右击您的生产项目>添加>控制器>选择 API 控制器清空。

在其中编写以下代码:

```
 private readonly List<Post> _posts;
public BlogController(List<Post> posts)
{
   _posts = posts;
}
public ActionResult<List<Post>> Get()
{
   return _posts.ToList();
} 
```

Enter fullscreen mode Exit fullscreen mode

此代码是给出 Post 列表的实际实现。 **Get** 方法返回之前定义的列表 **_posts** 。

这个方法是我们在之前写的测试的 *act* 中调用的方法，明白吗？

*   现在我们应该设置好了，但是如果你的代码中有红色下划线，检查一下引用是否丢失；主要是因为文件在文件夹中。

*   如果没有红色下划线。再次运行测试。这一次，如果一切顺利，我们应该会通过。

# 结论

如果一切顺利，我们已经使用 dotnet core 按照 TDD 原则成功实现了一个 WebAPI。并且永远记住 TDD 的这些规则；编写用例的实现(测试)，确保它失败，然后编写足够的产品代码来通过测试，仅此而已。您应该继续为博客可能需要的剩余用例实现另一个测试。

我希望我对你有所帮助。这是 github repo 的链接，我实际上在那里做了很多测试。[https://github.com/mrsimi/BlogAPIusingTDD](https://github.com/mrsimi/BlogAPIusingTDD)

编码快乐！