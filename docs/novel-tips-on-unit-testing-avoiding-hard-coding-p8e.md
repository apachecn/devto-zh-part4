# 单元测试的新技巧——避免硬编码

> 原文：<https://dev.to/zakwillis/novel-tips-on-unit-testing-avoiding-hard-coding-p8e>

## 单元测试都是关于模仿和伪造的？

当我第一次看到一个模拟，我不知道这是什么。一旦我明白了它们是用来做什么的，那么我就更加困惑了；

1.  当数据可能没有任何业务意义时，为什么有人要将值硬编码到存根类中呢？
2.  为什么要花这么多时间创建存根呢？
3.  我们知道单元测试是为了避免外部依赖而设计的，但是在这样做的时候，我们最终会得到一大堆对功能测试人员不可见的硬编码值。
4.  单元测试本质上是深奥的。

从根本上来说，似乎有太多的时间花在手工制作虚构的数据上，这些数据后来需要随着更多的商业知识变得明显而改变。

# 使单元测试更有用

请尽量避免对什么是单元测试和什么是集成测试感到不安。那些对定义特别精确的人不会太高兴。

## 使用 NUnit 的特性

### 使用类别

很多人可能不知道，我们可以对测试进行分类。我倾向于将它们命名为:

1.  单元测试。
2.  轻量测试。
3.  集成测试。有些人可能不知道测试资源管理器窗口允许您运行测试套件。更有用的是像 NUnit Test Runner 这样的测试运行器将让您运行一组特定的测试。这对于持续集成来说非常好。

```
[Category("Integration Test")] 
```

### 使用测试自变量作为断言

这个特别有用。这向开发者展示了我们期望的结果。

```
[Category("Unit Test")]
        [TestCase("£", "$", nameof(TokenBoundaryCharacterType.BothDifferent))]
        [TestCase("£", "", nameof(TokenBoundaryCharacterType.StartOnly))]
        [TestCase("", "£", nameof(TokenBoundaryCharacterType.EndOnly))]
        [TestCase("£", "£", nameof(TokenBoundaryCharacterType.SameStartAndEnd))]
        [TestCase("", "", nameof(TokenBoundaryCharacterType.CantClassify))]
        public void TestBoundaryStartEndsAreCorrect(string Start, String End, string TokenBoundaryCharacterType)
        {
            IRuleRetriever tokenBoundaryDetector = serviceProvider.GetRequiredService<IRuleRetriever>();

            var tokenBoundaryCharacterType = TokenBoundaryCharacterType.GetEnumFromText<TokenBoundaryCharacterType>();

            var result = tokenBoundaryDetector.DetermineTokenBoundaryCharacterType(new typTokenBoundary()
            { StartCharacter = Start, EndCharacter = End });

            Assert.AreEqual(tokenBoundaryCharacterType, result);
        } 
```

### 测试用例

```
 [TestCase("", "", nameof(TokenBoundaryCharacterType.CantClassify))]
        public void TestBoundaryStartEndsAreCorrect(string Start, String End, string TokenBoundaryCharacterType) 
```

您将在前面看到这一点，但是通过使用测试用例，您可以针对不同的条件多次运行相同的代码。

## 使用序列化和反序列化(英国非美国拼写)。

我发现创建有用测试的一个最重要的特性是将测试数据完全保存在应用程序之外。人们会声称这是在制造外部依赖，但谁在乎呢？

事情是这样的。
1)。将数据存储在数据库表或其他结构中。
2)。检索数据并将其序列化。
③。将它保存在磁盘上的某个地方，可能是一个测试文件夹。
4)。无论何时运行测试，请访问该文件。

我们可能会问，我们为什么要这样做？如果文件不在那里，或者数据不是预期的，我们就有测试失败的风险。关于为什么我们应该开始考虑序列化和去序列化对象，这里有一些主要的优点；

*   通过与测试人员合作，他们可以将文件放在一个目录中来测试他们的条件。也就是说，他们可以生成测试用例，将它们导出为 json。
*   应用程序可能能够生成测试数据。
*   减少了硬编码。
*   更多可见性/更少黑盒测试。

### 序列化/反序列化示例测试

这是不完整的，一些代码已经被省略。我用 FakeItEasy、NewtonSoft 和 NUnit 做了这些测试。你会注意到我没有任何断言，因为它更多的是测试概念而不是断言。

```
namespace IRTest.PublisherTests
{

    public class ExportSetting
        {
        public string FolderPath { get; set; }
        public string FileName { get; set; }
        public string ExportName { get; set; }

    }

    [TestFixture]
    public class TestWebsitePublication
    {
        private IServerConfig serverConfig { get; set; }

        public ExportSetting exportSetting { get; set; }

        private ServiceProvider serviceProvider { get; set; }

        [SetUp]
        public void SetUp()
        {
            var services = new ServiceCollection();

            SetupSetting();

            services.AddSingleton<IServerConfig>(serverConfig);

            services.AddTransient<IGetActivePackagePublication, GetActivePackagePublication>();

            services.AddTransient<IGetActivePackagePublicationEndPoint, GetActivePackagePublicationEndPoint>();

            services.AddTransient<IServiceGetReportsAndPublications, ServiceGetReportsAndPublications>();

            services.AddTransient<ISimpleFileCache, SimpleFileCache>();

            serviceProvider = services.BuildServiceProvider();

        }

        public void SetupSetting()
        {
            var config = new ConfigurationBuilder()
        .AddJsonFile("appsettings.json")
        .Build();

            var setting = new ServerConfig()
            {
                ConnectionString = config.GetConnectionString("PublishWebDB")
            };

            this.serverConfig = setting;

            this.exportSetting = new ExportSetting()
            {
                ExportName = "Cache"
                 , FileName = "AllPublicationAndReports.json"
                 , FolderPath = @"C:\InfoRhino\Test\Publication\"
            };

        }

        [Test]
        [Category("Integration Test")]
        public void OutputStaticDataSet()
        {

            PublishGetPublicationAndReports();

        }

        public void PublishGetPublicationAndReports()
        {
            var datarepo = serviceProvider.GetRequiredService<IServiceGetReportsAndPublications>();

            var data = datarepo.GetAllPublicationAndReports(DateTime.Now);

            var cacheWriter = serviceProvider.GetRequiredService<ISimpleFileCache>();

            cacheWriter.WriteObjectToFile(data, exportSetting.FolderPath, exportSetting.FileName, exportSetting.ExportName);
        }

        public IEnumerable<PublicationAndReports> GetPublicationAndReports()
        {

            var datarepo = serviceProvider.GetRequiredService<IServiceGetReportsAndPublications>();

            var cacheWriter = serviceProvider.GetRequiredService<ISimpleFileCache>();

            var fullFile = $"{exportSetting.FolderPath}\\{exportSetting.ExportName}\\{exportSetting.FileName}";

            if (!(System.IO.File.Exists(fullFile)))
            {
                PublishGetPublicationAndReports();
            }

            var data = cacheWriter.ReadObjectFromFile<IEnumerable<PublicationAndReports>>(
                $"{exportSetting.FolderPath}\\{exportSetting.ExportName}", exportSetting.FileName);

            return data;

        }

        public IDistributedCache GetCacheItem(byte[] textFromData)
        {

            var distCache = A.Fake<IDistributedCache>();

            A.CallTo(() => distCache.Get("Hello"))
                .Returns(textFromData);

            return distCache;
        }

        [Test]
        public void CheckData()
        {

            var data = GetPublicationAndReports();

            var textFromData = System.Text.UTF8Encoding.Unicode.GetBytes( data.First().Serialise());

            var cache = GetCacheItem(textFromData);

            var returnedText = cache.Get("Hello");
        } 
```

### 解释代码

没有太多的问题。我们伪造缓存，声明如果一个 get 方法被返回，我们将返回什么数据。FakeItEasy 不能使用扩展方法，因此我不使用 GetString 方法。

我有一个外部库用于存储工作，还有一个助手用于序列化和反序列化磁盘。
安装方法是中的微软依赖注入方法。网芯。我不是这个的最大粉丝，但是它很有效。
有某种讨厌的字符串废话，但可以整理。
我猜 CheckData 方法可以有一个断言。

对我来说，这足以让我在不依赖数据库表的情况下看到应用程序外部的测试数据。

越有意识的人会意识到，这样做的一个关键优势是，我们可以将数据库中的数据保存到文件中，这样就不用再担心数据库了。

### 提高质量

*   嗯，我可能会在存储库/数据访问层周围放一个包装器来使用缓存数据源。
*   断言是必须的。
*   IDistributedCache 协定的更多代码覆盖率。
*   将硬编码移到 appsettings.json 文件中。再说一次，现在这样不好。

```
 public void SetupSetting()
        {
            var config = new ConfigurationBuilder()
        .AddJsonFile("appsettings.json")
        .Build();

            var setting = new ServerConfig()
            {
                ConnectionString = config.GetConnectionString("PublishWebDB")
            };

            this.serverConfig = setting;

            this.exportSetting = new ExportSetting()
            {
                ExportName = "Cache"
                 , FileName = "AllPublicationAndReports.json"
                 , FolderPath = @"C:\InfoRhino\Test\Publication\"
            };

        } 
```

## 结论

如果我需要写代码，我可能会用一个更现实的例子来跟进，但希望它可以帮助你以不同的方式进行单元测试。我想大多数人会认为这是一个糟糕的方法，但我是一个数据人，这意味着我希望能够尽可能多地看到代码库之外的东西。