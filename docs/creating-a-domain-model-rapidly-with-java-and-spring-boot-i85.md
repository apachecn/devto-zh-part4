# 用 Java 和 Spring Boot 快速创建领域模型

> 原文：<https://dev.to/colaru/creating-a-domain-model-rapidly-with-java-and-spring-boot-i85>

**概述**

在这篇短文中，我们将为一个简单的应用程序创建一个领域模型。我们说明了我们如何基于应用程序业务描述来创建模型的类/对象。我们坚持认为实体是模型的一部分。

在这里，我们讨论一个具有客户机-服务器架构的 Web 应用程序的领域建模，其中我们在服务器端(后端)有 Java。所有的例子都是 Java 语言。我们使用 Spring Boot 来加速应用程序的启动。

**开始实施**

我们正在使用的工具:

*   [Java 8 JDK 来自 AdoptOpenJDK](https://adoptopenjdk.net/) 安装有 [SdkMan](https://sdkman.io/)
*   [SpringBoot](https://spring.io/projects/spring-boot) -只为使用 [Spring Starter](https://start.spring.io/) 快速启动一个集成构建的 app
*   [Maven](https://maven.apache.org/) -在内部用于构建
*   [JUnit](https://mvnrepository.com/artifact/junit/junit) -用于玩域
*   [Intellij Idea](https://www.jetbrains.com/idea/) -用于代码编辑(可以使用 [VS 代码](https://code.visualstudio.com/)或者 [Sublime](https://www.sublimetext.com/)

如果你使用的是 Windows 操作系统，那么 Java JDK 8 可以手动安装，Maven 也可以手动安装。spring Initializr-[https://start . spring . io](https://start.spring.io)可以用来下载 Spring Boot 入门 app。但是在 MAC OS 和 Linux 上，最快的方法是使用终端命令，就像你在后面看到的那样。

启动我们的应用程序(我们将其命名为**cindykat**——一个非常接近**辛迪加**的名字)并运行它就像这样:

```
# install SDKMAN
curl -s "https://get.sdkman.io" | bash
# install JDK
sdk install 8.0.212.j9-adpt
# create app
curl https://start.spring.io/starter.zip -d name=CindyKat -d groupId=com.colaru -d artifactIf=cindykat -d packageName=com.colaru.cindykat -d dependencies=web -d javaVersion=8 -o cindykat-springboot.zip
# unzip, then run it
unzip cindykat-springboot.zip -d cindykat-springboot
cd cindykat-springboot && ./mvnw spring-boot:run 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的应用程序已经在 8080 上运行了——这是一个 web 应用程序！不需要网络应用程序——启动并运行一些东西就可以了。

[![](img/64b0fba9d5cebb1860995fc23b009a61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UBHWkHtE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t02cv9gfijdson5jyya0.png)

我们有这样的项目结构:

[![](img/89d92c319bdfeaacf6c1aa63b713305a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QN1YACQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/we6h0n6qvufymv0avawx.png)

**什么是应用领域？**

领域模型是我们代码中的一部分，在这里我们对应用程序的业务进行建模。通常，应用程序模型必须是它所实现的业务的直接反映。我们的代码必须是我们正在建模的现实的故事。我们必须使用一种所有参与这个项目的人都使用的通用语言。

没有必要引入不同于我们正在建模的现实中的其他抽象。业务中的名词和动词必须出现在我们的类、字段和方法的名称中。当我们引入其他东西时，我们可能会被怀疑过度设计。

[![Domain model](img/48815c6afb3b8730292b0406ccee8392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yhfFma49--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s5xur8r8awydi8dttbs3.png) 
图片来源:[https://www . slide share . net/Dennis _ Traub/dotnetcologne 2013-DDD](https://www.slideshare.net/Dennis_Traub/dotnetcologne2013-ddd)

为什么模型如此重要？因为应用程序的所有其余部分都将与模型接触。由于编组/解编的复杂性，很难有单独的 d to 或 WS REST 资源或持久性实体包。如果我们不想将内部模型暴露在系统之外，我们将被迫这样做。因此，我们将在应用程序的所有层中使用该模型。您可以确定 UI、持久性、报告、WS、消息传递集成都在使用域模型类。

随着时间的推移，模型会变得越来越复杂(数百个类)。模型中的内容将会发展并影响整个应用程序。这是新开发团队成员在任何应用程序中最难理解的部分。这不像是在其他应用程序中重用的库或框架，而是特定于业务的东西。业务可能非常非常复杂。

**DDD 的书**

有几种方法可以启动 Java Web 应用程序:

*   后端优先——使用测试创建一个域模型，这是服务器端的核心
*   首先是前端——用 HTML/ CSS/ JavaScript 从 UX/UI 开始，创建一些演示用户交互的模型——你也可以在 JSON 或 Typescript 中使用域模型

通常有一个团队在前端工作，另一个团队在后端工作，他们之间的合同是一个 Swagger/Open API 规范。

第一种方法，领域建模，是本文的主题。这也是一本非常著名的书的主题:[领域驱动设计](https://dddcommunity.org/book/evans_2003/)作者 Eric Evans。在本书中，除了本文中描述的图形实体之外，还介绍了更多的主题。

[![](img/4a35bbda45315e01f155c5042cccb331.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4rJDYxI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yo6e7bamp6rdc61bvomk.png)

图片来自[领域驱动设计快速](https://www.infoq.com/minibooks/domain-driven-design-quickly/#minibookDownload,%20/minibooks/domain-driven-design-quickly/#minibookDownload/)书

**我们的应用业务讲解**

我们想要实现的应用程序是一个由 [Google Trending Searches](https://trends.google.com/trends/trendingsearches/daily?geo=US) 提供的数据的分析系统——由 Google 每天和每个国家报告的来自 Google Search 的搜索历史。我们想从谷歌趋势导入一些数据，然后将其存储在我们的系统中，并使其有可能分析它，以不同的形式显示它，等等。

[![](img/126dfafb976a515044150e929e69c96d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s5nw6TlX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmivpmsneidd0zubyxjw.png)

在这种情况下，对我们的分析系统中使用的领域进行建模将会很简单。因为我们将实体与用于获取搜索数据的格式区分开来。查看这个 Google Trends**Atom Syndication Format**XML 片段:

```
 <item>
            Women's World Cup 2019
            <ht:approx_traffic>1,000,000+</ht:approx_traffic>
            <description>2019 Women, Women World Cup, WWC</description>
            <link>https://trends.google.com/trends/trendingsearches/daily?geo=US#Women's%20World%20Cup%202019</link>
            <pubDate>Mon, 10 Jun 2019 22:00:00 -0700</pubDate>
            <ht:picture>https://t2.gstatic.com/images?q=tbn:ANd9GcTW4UzPHNC9qjHRxBr6kCUEns71l8XK6HYcmLpJbhlfZWUbeBQPiia1GDzN3Ehl7nfD-HPbgnG_</ht:picture>
            <ht:picture_source>CBSSports.com</ht:picture_source>
            <ht:news_item>
                <ht:news_item_title>&lt;b&gt;2019 Women&amp;#39;s World Cup&lt;/b&gt; scores, highlights: Canada squeaks by, Japan underwhelms, Argentina gets historic point</ht:news_item_title>
                <ht:news_item_snippet>Day 4 of the &lt;b&gt;2019&lt;/b&gt; FIFA &lt;b&gt;Women&amp;#39;s World Cup&lt;/b&gt; in France featured a two-game slate with two potential contenders opening their campaigns against slightly inferior opponents. When the dust settled, neither team looked particularly sharp as only one goal was&amp;nbsp;...</ht:news_item_snippet>
                <ht:news_item_url>https://www.cbssports.com/soccer/world-cup/news/2019-womens-world-cup-scores-highlights-canada-squeaks-by-japan-underwhelms-argentina-gets-historic-point/</ht:news_item_url>
                <ht:news_item_source>CBSSports.com</ht:news_item_source>
            </ht:news_item>
            <ht:news_item>
                <ht:news_item_title>&lt;b&gt;2019 Women&amp;#39;s World Cup&lt;/b&gt; scores, highlights, recap: Japan underwhelms in opener as Argentina gets historic point</ht:news_item_title>
                <ht:news_item_snippet>Day 4 of the &lt;b&gt;2019 World Cup&lt;/b&gt; has a small slate of action with just two games, but two contenders to win the tournament were scheduled to play their opener. After the first match, we may just be talking about one contender. With talented Canada set to play&amp;nbsp;...</ht:news_item_snippet>
                <ht:news_item_url>https://www.cbssports.com/soccer/world-cup/news/2019-womens-world-cup-scores-highlights-recap-japan-underwhelms-in-opener-as-argentina-gets-historic-point/</ht:news_item_url>
                <ht:news_item_source>CBSSports.com</ht:news_item_source>
            </ht:news_item>
        </item> 
```

Enter fullscreen mode Exit fullscreen mode

所以很简单！我们有一个**条目**，它有一个**新闻条目**的列表。我们单独拥有一个**源**实体，它将在**新闻条目**之间被重用。另外，**项**需要一个**国家**指定国家/语言。仅此而已。

[![](img/700247f5cdcf6c50252254c7436ee3ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mtZcqiwi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zymr3toj8b4ktrxg4y4r.png)

**创建实体图**

领域模型将是我们应用程序中的一个单独的包。我们选择创建一个名为 **newsfeed** (全名**com . colaru . Cindy kat . domain . news feed)**的子域来命名包，因为将来可能会有其他聚合(实体组)。

为域创建一个完整的模块是一个好主意(在多模块 Maven/Gradle 项目中),因为这样我们可以在应用程序的任何其他模块中创建对它的依赖——所有其他模块都将使用域模型。

首先，使用 IDE，我们将创建一个 **Item** 类，它是主要实体(DDD 术语中的聚合根),然后是其余的类:

```
package com.colaru.cindykat.domain.newsfeed;

import java.util.Date;
import java.util.List;

public class Item {

    private String title;
    private List<Tag> description;
    private String link;
    private String picture;

    private Date pubDate;
    private String pubDateAsString;

    private String approxTraffic;
    private Long approxTrafficAsNumber;

    private List<NewsItem> items;
    private Country country;

    // generate getters/setters using the IDE
}

public class NewsItem {
    private String title;
    private String snippet;
    private String url;
    private Source source;
}

public class NewsItem {
    private String title;
    private String snippet;
    private String url;
    private Source source;
}

public class Country {
    private String name;
    private String countryCode;
    private String languageCode;
    private String flag;
}

public class Source {
    private String name;
    private String url;
}

public class Tag {
    private String name;
} 
```

Enter fullscreen mode Exit fullscreen mode

最终，我们会得到这个实体图:

[![Entities graph](img/5e296665b474a7d0a046f2843618c1ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iY6-1HjU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqhni36nv8u1na2azhv1.png)

现在项目文件系统是这样的:

[![Project structure](img/d733d1bf6abb0f562a47f64e171da870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bM9AY0ZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p40ikwb4k3vn1501m6da.png)

**贫血域**

领域的一个常见问题是由 Martin Fowler 命名为[贫血域的反模式——类只有状态而没有行为。我们从真实的单词中只能得到**名词**，而不能得到**动词**。在这种情况下，实体是像函数式编程语言中的数据结构，而不是真正的 Java 对象。这很正常，因为一般来说，实体是 ORM 持久性框架使用的数据库表的映射器。](https://martinfowler.com/bliki/AnemicDomainModel.html)

我们期望**动词**将在域外的应用程序的服务层中。但是在这种情况下，另一个讨论开始了:这些服务是域的一部分吗？

但是在实体中直接引入一些逻辑也没有问题——在这种情况下，我们必须意识到我们将在两个地方有业务逻辑:服务和领域模型。

我们将在**项**实体中介绍一些业务方法(一些从字符串到日期和从字符串到长型的转换器):

```
public class Item {
        private Date pubDate;
    private String pubDateAsString;
    private String approxTraffic;
    private Long approxTrafficAsNumber;

    // other private fields

    public Date convertStringToDate(String pubDateAsString) throws ParseException {
        SimpleDateFormat parser = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss Z"); // Wed, 21 Dec 2016 13:00:00 +0200
        return parser.parse(pubDateAsString);
    }

    public Long convertStringToLong(String approxTraffic) {
        return new Long(approxTraffic.toString().replaceAll(",", "").replace("+", ""));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**测试域**

即使领域很简单，使用 JUnit 开始使用它并创建一些测试也是一个好主意。首先，我们必须将 JUnit 库作为我们的 Maven 依赖项的一部分包含在 pom.xml 中:

```
 <!-- https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-api -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.3.2</version>
        <scope>test</scope>
    </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建第一个测试来玩我们简单的对象图，并验证一些小功能。这是第一个足够好的工作框架，可以在测试的指导下开始我们的 TDD 开发，我们将在这个应用程序中添加以下功能:

```
class DomainModelTests {

    private Item item;

    @BeforeEach
    void setUp() throws ParseException {
        item = new Item();
        item.setTitle("Women's World Cup 2019");

        item.setLink("https://trends.google.com/trends/trendingsearches/daily?geo=US#Women's%20World%20Cup%202019");
        item.setPicture("https://t2.gstatic.com/images?q=tbn:ANd9GcTW4UzPHNC9qjHRxBr6kCUEns71l8XK6HYcmLpJbhlfZWUbeBQPiia1GDzN3Ehl7nfD-HPbgnG_");

        // tags
        Tag tag = new Tag();
        tag.setName("Word cup");
        List<Tag> tags = new ArrayList<>();
        tags.add(tag);
        item.setDescription(tags);

        NewsItem newsItem = new NewsItem();
        // source
        Source source = new Source();
        source.setName("USA TODAY");
        newsItem.setSource(source);
        newsItem.setTitle("&lt;b&gt;2019 Women&amp;#39;s World Cup&lt;/b&gt; scores, highlights: Canada squeaks by, Japan underwhelms, Argentina gets historic point");
        List<NewsItem> items = new ArrayList<>();
        items.add(newsItem);
        item.setItems(items);
    }

    @Test
    void buildNewItemTest()  {
        Assert.assertEquals(1, item.getItems().size());
        Assert.assertEquals(1, item.getDescription().size());
    }

    @Test
    void convertStringToLongTest() {
        String approxTraffic = "900,000+";
        item.setApproxTraffic(approxTraffic);
        item.setApproxTrafficAsNumber(item.convertStringToLong(approxTraffic));
        Assert.assertEquals(900000, item.getApproxTrafficAsNumber().longValue());
    }

    @Test
    void convertStringToDateTest() {
        String pubDateAsString = "Mon, 1 Jun 2020 09:00:00 -0700";
        item.setPubDateAsString(pubDateAsString);
        try {
            item.setPubDate(item.convertStringToDate(pubDateAsString));
        } catch (ParseException e) {
            e.printStackTrace();
        }
        Calendar cal = new Calendar.Builder().setCalendarType("iso8601")
                .setFields(YEAR, 2020, DAY_OF_MONTH, 1, MONTH, 5, HOUR, 18, MINUTE, 0, SECOND, 0)
                .build();

        Assert.assertEquals(cal.getTime(), item.getPubDate());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行所有测试时，条是绿色的(我首先编写测试，然后是被测试的方法，测试运行失败，我添加实现，我再次运行测试，现在它通过了):

[![](img/144786664d8a6f2eada9464fff93a6a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oq-EaOVe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rwxmsnd8qsw3b42m9t75.png)

**结论**

在本文中，我们展示了如何为一个小应用程序创建一个简单的模型。

正如我们所看到的，领域模型是应用程序的核心部分。组成领域模型的这些类是技术不可知的(并发性、持久性不是我们现在感兴趣的)——只是描述了业务的现实——与项目中使用的技术无关。

**Git 仓库**

我们在 GitHub 上公布了来源:

```
git clone https://github.com/colaru/cindykat-springboot.git
cd cindykat-springboot
mvn clean install // the tests will be executed successfully 
```

Enter fullscreen mode Exit fullscreen mode

* * *

你可以在推特上关注我，我会继续记录我的旅程。

* * *

**本文的灵感链接**

*   价值对象 vs 实体:[https://enterprisescrafts . com/2016/01/11/entity-vs-value-object-the-ultimate-list-of-differences/](https://enterprisecraftsmanship.com/2016/01/11/entity-vs-value-object-the-ultimate-list-of-differences/)
*   贫血域:[https://martinfowler.com/bliki/AnemicDomainModel.html](https://martinfowler.com/bliki/AnemicDomainModel.html)
*   域服务:[https://stack overflow . com/questions/2268699/domain-driven-design-domain-service-application-service](https://stackoverflow.com/questions/2268699/domain-driven-design-domain-service-application-service)
*   域名型号:[https://martinfowler.com/eaaCatalog/domainModel.html](https://martinfowler.com/eaaCatalog/domainModel.html)
*   服务层:[https://martinfowler.com/eaaCatalog/serviceLayer.html](https://martinfowler.com/eaaCatalog/serviceLayer.html)
*   对象和数据结构:[https://blog . clean coder . com/uncle-bob/2019/06/16/objectsanddatastructures . html](https://blog.cleancoder.com/uncle-bob/2019/06/16/ObjectsAndDataStructures.html)
*   领域驱动设计书作者 Eric Evans:[https://dddcommunity.org/book/evans_2003/](https://dddcommunity.org/book/evans_2003/)
*   域驱动设计快速预定:[https://www . infoq . com/mini books/domain-Driven-Design-Quickly/# mini book download，/mini books/domain-Driven-Design-Quickly/# mini book download/](https://www.infoq.com/minibooks/domain-driven-design-quickly/#minibookDownload,%20/minibooks/domain-driven-design-quickly/#minibookDownload/)
*   Bob 大叔的数据结构 vs 对象:[https://blog . clean coder . com/Uncle-Bob/2019/06/16/objectsanddatastructures . html](https://blog.cleancoder.com/uncle-bob/2019/06/16/ObjectsAndDataStructures.html)
*   2019 年关于 Java 的 10 个神话:[https://developer.okta.com/blog/2019/07/15/java-myths-2019](https://developer.okta.com/blog/2019/07/15/java-myths-2019)
*   应该用哪个 Java SDK:[https://developer.okta.com/blog/2019/01/16/which-java-sdk](https://developer.okta.com/blog/2019/01/16/which-java-sdk)