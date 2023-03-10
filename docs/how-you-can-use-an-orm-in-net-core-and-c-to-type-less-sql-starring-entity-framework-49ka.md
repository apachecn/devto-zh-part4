# 如何在？NET Core 和 C#来键入更少的以 SQL 为主的实体框架

> 原文：<https://dev.to/dotnet/how-you-can-use-an-orm-in-net-core-and-c-to-type-less-sql-starring-entity-framework-49ka>

> 我们都爱 SQL，对吗？没有吗？有时候，一个好的 SQL 查询是最好的方法，但是大多数时候，你需要在每个新实体上执行相同的 CRUD 操作，创建、读取、更新、删除。有了 ORM，一个对象关系映射器，你就可以用代码定义数据库的结构。此外，您也可以在查询中使用代码。的主窗体。Net 和。Net 核心被称为实体框架，这就是我们在这篇文章中所涉及的。

我只想说一件非常重要的事情。像 ORM 这样的工具永远不应该取代学习 SQL。ORM 的存在是为了让你的生活更简单，所以当你最终编写 SQL 时，它是为了重要的事情，比如报告查询或需要真正高性能的查询。这个想法是让 ORM 处理更简单的 SQL，比如创建表和做简单的插入。如果你没有足够的 SQL 知识来使用它，那么请看看这里，试着先掌握基本知识:

> [https://www.w3schools.com/sql/](https://www.w3schools.com/sql/)

TLDR；这篇文章有点长，但它从头开始教你实体框架，涵盖了许多真正伟大的主题，值得一读。

在本文中，我们将涵盖:

*   为什么一个 ORM ，我们总是需要问自己为什么要用某个东西。如果你对数据库有很多简单的交互，ORM 真的会大放异彩。使用它你真的可以加快你的操作。
*   它能帮你做什么。
*   **安装**并设置
*   CRUD 演示。我们将经历读取数据、创建、更新和删除数据

## 资源

*   [数据库提供者](https://docs.microsoft.com/en-us/ef/core/providers/?wt.mc_id=devto-blog-chnoring)
    你可以使用实体框架与许多不同的数据库一起工作。整个想法是有一个不可知论的方法，所以理论上，你可以用一个数据库替换另一个，而你的代码保持不变。我们都知道我们几乎从不这样做，但这是一个好主意。

*   [初学英孚核心文章](https://docs.microsoft.com/en-us/ef/core/get-started/netcore/new-db-sqlite?wt.mc_id=devto-blog-chnoring)
    这篇文章部分基于这篇文章，即使我们更进一步

*   [在 ASP 中使用 EF 内核。净 MVC](https://docs.microsoft.com/en-us/aspnet/core/data/ef-mvc/intro?view=aspnetcore-2.2&wt.mc_id=devto-blog-chnoring)

*   渴望加载
    我们在文章中介绍了这种方式的基本原理，但是还有很多东西需要学习。

*   [查询基础](https://docs.microsoft.com/en-us/ef/core/querying/basic?wt.mc_id=devto-blog-chnoring)

*   [EF 核心一切概览页面](https://docs.microsoft.com/en-us/ef/core/?wt.mc_id=devto-blog-chnoring)

## 为什么是 ORM

使用 ORM 是为了变得更快、更有效率，以及确切地知道数据库中有什么。

> 那我什么时候用，总是还是？

对于大多数简单的应用程序来说，它绝对好用。对于真正需要高性能查询的应用程序，你仍然可以使用它，但是你需要更好地观察 ORM 产生的 SQL。有时这就足够了，有时您需要使用 SQL 手工编写这些查询。一般来说，我个人不会使用 ORM 来报告查询，因为它们往往很复杂，很难用代码来表达。但是每个人都不一样。我甚至见过用代码编写复杂的查询。

### 奥姆景观

对于. Net 有不止一种 ORM 选择。Entity Framework 是最广为人知的一种，但是还有其他的。你必须决定哪一个适合你的项目。

*   如果你只看语法，Linq 2 db
    提供了与实体框架相似的体验。有人说该语法接近实际 SQL 中的语法

*   [衣冠楚楚](https://dapper-tutorial.net/)
    有类似*对象映射器*和*微形器*的描述

*   。Hibernate 的 Net 端口。那里最古老的 ORM 之一。

这里有更多的 ORM，但是上面的三个是众所周知的选择。

## 什么

大多数 ORM 允许您用代码定义表结构，并且您可以映射一个类，使它对应于一个表。列只是类的属性。根据 ORM 的不同，有几种可能的方法

*   **Schema first** ，在这个场景中，您定义了一个您拥有的表的模式，它们是如何关联的，比如一对一、一对多、一对多等等。您最终会从模式中生成代码。
*   **代码优先**，在这种方法中，您首先定义代码。每个表对应一个类，你可以用代码来表达每件事之间的关系。然后，您的 ORM 将查看您的代码，并从中生成结构化 SQL。

### 迁徙

很多 ORM 都有一个叫做*迁移*的概念。迁移只是一段脚本，要么改变数据库的结构，要么运行一段影响数据的 SQL，例如*用一些初始数据播种*数据库。其思想是，每次您对数据库进行更改时，都应该在迁移中捕获一个小的事务性更改。然后可以将该迁移*应用*到数据库，从而以期望的方式改变数据库。例如，将客户表添加到数据库中是一种迁移，当应用这种迁移时，会在数据库中创建该表。迁移可以用 SQL 或代码来表达。

## 安装和设置

要开始使用实体框架，我们需要几个 NuGet 包，还需要一个可以安装 NuGet 包的项目。因此，在本练习中，我们将执行以下操作:

1.  **创建**解决方案
2.  **Scaffold** 一个控制台项目并添加对解决方案的引用
3.  **安装**需要的 NuGet 包到控制台项目

### 创建解决方案

这很容易做到。首先，我们需要一个目录。所以创建一个目录，你可以自己选择名字，但这里有一个例子。

```
mkdir demo 
```

然后，我们需要将自己放在目录中，如下所示:

```
cd demo 
```

### 脚手架控制台项目

接下来，我们需要创建我们的控制台项目。你也可以选择名字，但是我们用`App`。键入以下内容:

```
dotnet new console -o App 
```

这将创建一个名为`App`的类型为`console`的新项目。

最后，我们将这个项目添加到解决方案中，如下所示:

```
dotnet sln add App/App.csproj 
```

### 安装和设置

为此，我们将为实体框架安装核心库，同时也支持数据库类型 SqlLite。请注意，它支持不同的数据库，请在此查看支持的数据库的完整列表:

> [https://docs.microsoft.com/en-us/ef/core/providers/](https://docs.microsoft.com/en-us/ef/core/providers/?wt.mc_id=devto-blog-chnoring)

SqlLite 是一个非常简单的数据库，它只是将结构和数据存储在硬盘上的一个文件中。

> 但是我正在使用一个真实的数据库，我呢，我会从这篇文章中受益吗？

是的，我们展示的是通用知识，无论数据库类型如何，都可以广泛应用。

好吧，那么让我们首先进入我们的控制台应用程序目录，就像这样:

```
cd App 
```

然后安装需要的 NuGet 库:

```
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design 
```

这将向您的项目添加引用。打开`App.csproj`，你应该会发现类似这样的东西:

```
<ItemGroup>
  <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="2.2.6" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="2.2.6" />
</ItemGroup> 
```

现在我们需要实际安装这些库，我们使用下面的命令:

```
dotnet restore 
```

## 一个 CRUD 演示

我们将展示如何进行完整的 CRUD、创建、读取、更新和删除。

在这里，我们将尝试以下内容:

*   **创建**数据库
*   **创建一个表示数据库结构的迁移**,然后应用它来创建数据库
*   从数据库中读取
*   **将**写入数据库
*   **用初始数据播种**我们的数据库

### 创建数据库

首先，我们需要一个数据库，所以让我们创建一个。

我们将创建一个名为`Database.cs`的文件，内容如下:

```
// Database.cs

using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;

namespace App
{
  public class DatabaseContext : DbContext
  {
    public DbSet<Product> Products { get; set; }

    public DbSet<OrderItem> OrderItems { get; set; }

    public DbSet<Order> Orders { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlite("Data Source=database.db");
    }
  }

  public class Order
  {
    public int OrderId { get; set; }

    public DateTime? Created { get; set; }

    public ICollection<OrderItem> Items { get; set; }
  }

  public class OrderItem
  {
    public int OrderItemId { get; set; }
    public int Quantity { get; set; }
    public virtual Product Product { get; set; }
  }

  public class Product 
  {
    public int ProductId { get; set; }
    public double Price { get; set; }
    public string Description { get; set; }
  }
} 
```

从上面的代码中可以看出，我们有以下几个类:

*   **订单**，这是一个代表订单的类。
*   **OrderItem** ，一个订单有许多 OrderItem，每个 OrderItem 都有一个`Quantity`属性和对一个`Product`的引用
*   **产品**，这代表我们要订购的产品。它上面有像`Price`和`Description`这样的信息。

让我们评论一下代码中一些有趣的结构。

**一对多**

我们通过下面的`Order`类的构造来表达一对多的关系:

```
public ICollection<OrderItem> Items { get; set; } 
```

上面我们说我们在订单上有一个订单条目列表。

**外键**

我们还表达了另一个数据库概念，即*外键*。在`OrderItem`实体中，我们说我们引用了一个产品。在代码中，我们这样写:

```
public virtual Product Product { get; set; } 
```

**数据库上下文和数据库集**

先来评论一下`DbContext`。当我们需要一个新的数据库时，我们应该像这样继承这个类:

```
public class DatabaseContext : DbContext 
```

`DbSet`表示数据库中的一个表。这是一个泛型，它将一个`type`作为模板参数，比如:

```
public DbSet<OrderItem> OrderItems { get; set; } 
```

### 创建一个迁移

现在我们已经保存了我们的文件`Database.cs`。是时候创建数据库了。为此，我们需要做两件事:

*   **生成一个迁移**，这将获取代码当前状态的快照，并将其与之前的快照进行比较。如果它没有以前的快照，生成迁移将简单地创建*初始迁移*。

*   **应用迁移**，这将运行迁移。根据迁移的内容，它将创建一个数据库，影响数据库结构或改变数据。

**生成迁移**

让我们用下面的命令创建我们的迁移:

```
dotnet ef migrations add InitialCreate 
```

最后一个参数是迁移的名称，我们可以随意命名，但最好给它一个描述性的名称，如`InitialCreate`。

运行该命令应该会在终端中产生以下结果:

[![](img/799b47d55e99872c8773f1e4973a8b43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eKtorS5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfk5a3yk4h8b0ovdaoh6.png)

正如你在上面看到的，它很好地告诉我们如何用命令`ef migrations remove`撤销我们刚刚做的事情。

这为我们创建了一些文件，即:

[![](img/a901f2448617b004069232cfa939453c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---wTzmpSN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rh7ugmlqutp59qu9yr7w.png)

在上面你可以看到我们得到了我们的迁移`InitialCreate`,但是名字前面加了一个时间戳。这就是为什么实体框架知道运行什么和以什么顺序运行。我们还可以看到这个文件有两个版本，一个是*。cs* 和一个`Designer.cs`文件。我们只关心第一个。让我们来看看:

```
using System;
using Microsoft.EntityFrameworkCore.Migrations;

namespace App.Migrations
{
    public partial class InitialCreate : Migration
    {
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.CreateTable(
                name: "Orders",
                columns: table => new
                {
                    OrderId = table.Column<int>(nullable: false)
                        .Annotation("Sqlite:Autoincrement", true),
                    Created = table.Column<DateTime>(nullable: true)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_Orders", x => x.OrderId);
                });

            migrationBuilder.CreateTable(
                name: "Products",
                columns: table => new
                {
                    ProductId = table.Column<int>(nullable: false)
                        .Annotation("Sqlite:Autoincrement", true),
                    Price = table.Column<double>(nullable: false),
                    Description = table.Column<string>(nullable: true)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_Products", x => x.ProductId);
                });

            migrationBuilder.CreateTable(
                name: "OrderItems",
                columns: table => new
                {
                    OrderItemId = table.Column<int>(nullable: false)
                        .Annotation("Sqlite:Autoincrement", true),
                    Quantity = table.Column<int>(nullable: false),
                    ProductId = table.Column<int>(nullable: true),
                    OrderId = table.Column<int>(nullable: true)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_OrderItems", x => x.OrderItemId);
                    table.ForeignKey(
                        name: "FK_OrderItems_Orders_OrderId",
                        column: x => x.OrderId,
                        principalTable: "Orders",
                        principalColumn: "OrderId",
                        onDelete: ReferentialAction.Restrict);
                    table.ForeignKey(
                        name: "FK_OrderItems_Products_ProductId",
                        column: x => x.ProductId,
                        principalTable: "Products",
                        principalColumn: "ProductId",
                        onDelete: ReferentialAction.Restrict);
                });

            migrationBuilder.CreateIndex(
                name: "IX_OrderItems_OrderId",
                table: "OrderItems",
                column: "OrderId");

            migrationBuilder.CreateIndex(
                name: "IX_OrderItems_ProductId",
                table: "OrderItems",
                column: "ProductId");
        }

        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.DropTable(
                name: "OrderItems");

            migrationBuilder.DropTable(
                name: "Orders");

            migrationBuilder.DropTable(
                name: "Products");
        }
    }
} 
```

我们首先看到的是我们从类`Migration`继承而来。第二件事是我们有两个方法`Up()`和`Down()`。`Up()`是在我们想应用什么东西的时候运行的。`Down()`是在我们想要*撤销*迁移时运行的。查看我们的`Up()`方法，我们可以看到我们为每个表`Order`、`OrderItem`和`Product`调用了一次`CreateTable()`。我们还可以看到它定义了所有需要的外键。`Down()`方法调用`DropTable()`来*撤销*我们的表创建。

**应用迁移**

好了，我们有一个迁移，让我们*应用*它。我们使用下面的命令来完成这个任务:

```
dotnet ef database update 
```

如果需要，这将首先创建数据库，然后应用迁移。

[![](img/f2f213edc90f34df8c274b10339a09b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6AN-Z6SU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/topziid6t8af3o4bwwci.png)

我们可以在文件结构中看到，我们创建了一个新文件`database.db`。我们可以使用 SQlLite 客户端，或者为什么不写一些代码来连接它呢？:)

## 从数据库中读取

好了，现在我们想看看是否能连接到我们的数据库，也许能读出一些数据。打开`Program.cs`并转到方法`Main()`并添加以下内容:

```
using (var db = new DatabaseContext())
{
} 
```

这将建立到我们数据库的连接。要从数据库中读取数据，我们只需要像这样读取:

```
using (var db = new DatabaseContext())
{
  var orders = db.Orders;
  foreach(var order in orders)
  {
    Console.WriteLine("Order: order.Created");
  }
} 
```

我们可以试试吗？

[![](img/07e186e45e8495b903145112c777d50b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L5ByQ0O1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pe8gwgvdq3myct922qhi.png)

好吧，我们没有订单。

嗯，这是意料之中的，我们没有在数据库里放任何东西。我们改变一下怎么样？

## 写入数据库

好的，我们知道如何连接数据库。给它写信怎么样？

为了能够创建一个`Order`，我们首先需要一些数据，至少有一个`Product`和一个`OrderItem`。如果你想保存一些东西到数据库，你需要调用`db.SaveChanges()`。

我们需要一步一步来，因为有一些移动的部分。

### 创建产品

首先，我们将创建一个`Product`。

让我们添加下面的代码:

```
using (var db = new DatabaseContext())
{

    var product = new Product(){ Price = 100, Description = "Movie" };
    db.Products.Add(product);
    db.SaveChanges();

    foreach(var p in db.Products) 
    {
        Console.WriteLine("{0} {1} {2}", p.ProductId, p.Description, p.Price);
    }

} 
```

上面将创建我们的`Product`,通过调用`db.SaveChanges()`,我们确保将它保存到数据库中。

运行代码会导致

[![](img/9643c63613af42e1b052853f0e4fe806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ik_okX5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3mmuogs3rswgof67f0my.png)

**订单项目**

好吧，这部分有用。创建一个`OrderItem`怎么样？这很简单，我们只需要下面的代码:

```
using (var db = new DatabaseContext())
{
    var product = db.Products.SingleOrDefault();

    if(product != null)
    {
        var item = new OrderItem
        {
            Quantity = 1,
            Product = product
        };
        db.OrderItems.Add(item);
        db.SaveChanges();

        Console.WriteLine("{0} {1} Product: {2}", item.OrderItemId, item.Quantity, item.Product.Description);
    }
} 
```

让我们试着突出重要的部分。

[![](img/37d7245277c26c4b6489767e3ec40f9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3j4T7ft3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h1rn0g903qcu5k0h9gyw.png)

上面我们可以看到，我们首先从数据库中读出一个`product`。我们要做的下一件事是将同一个产品分配给`OrderItem`上的`Product`属性。然后我们通过将我们的`OrderItem`添加到`db.OrderItems`中，然后调用`db.SaveChanges()`来保存它。

### 创建订单

到目前为止，我们在数据库中已经有了一个`Product`和一个`OrderItem`。那么，我们如何着手创建包含这两个实体的订单呢？

创建订单不仅仅是创建一个订单，而是创建一个订单并将订单项与订单关联起来。

关联部分可以通过两种不同的方式完成:

1.  将订单项添加到`order.Items`
2.  向 OrderItem 添加一个外键，并分配订单 id。

以上两种解决方案都要求我们对实体框架有更多的了解。

**加载相关实体**

让我们从第一种方法开始。为此，我们需要知道如何加载相关的实体。

> 为什么？

嗯，当你有一个`Order`实例时，它的`Items`将是`null`，除非我们明确地告诉它要填充一些东西。为了让这种方法工作，我们需要它至少是一个空列表，这样我们就可以添加我们的`OrderItem`。

> 好吧，我想你最好给我看看。

当然，看看下面的代码:

```
var item = db.OrderItems.SingleOrDefault();
var order = new Order() { Created = DateTime.Now };
db.Orders.Add(order);
db.SaveChanges(); 
```

这就产生了一个订单。加我们的`item`怎么样？嗯，我们有个问题:

[![](img/4448509b3db15257881dd2ab5747478f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--loN_dmpR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6k8fschsk4sx9ru1ff1f.png)

如果我们在第 49 行保存了我们的`Order`之后，试图调用我们的`item`，我们的`order.Items`将会是`null`，我们将会得到一个运行时异常。为了解决这个问题，我们需要使用`Include()`方法。`Include()`取一个 lambda，在这里我们需要指出我们想要加载的内容。在这种情况下，我们希望在我们的`Order`上加载属性`Items`。

让我们运行这段代码:

[![](img/bdd4eefe5a8547ba4723752a96243949.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oZrv5qbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43h25e3kb6t2e4r5zzue.png)

在这一点上，我们的`order.Items`是一个空数组，我们可以添加我们的`OrderItem`而不会像你看到的那样代码崩溃，因为我们到了第 54 行。

**添加一个外键到 OrderItem**

在幕后，我们已经在`OrderItem`上获得了一个外键。我们可以看到，如果我们开放迁移:

[![](img/f77a64aef613710bdead6cde9e1c93a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0mTmBt78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/izsvemi4ucxibcxdoljc.png)

我们现在的问题是，它在我们的`OrderItem`上并不存在，那么我们该如何解决这个问题呢？

我们刚刚在类定义中添加了:

[![](img/20e4d29d756931bdeb7c69f57e5d45ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jF052lfT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c4txnmgwgrjqit0mq4p9.png)

然后，因为我们有一个与 OrderItem 相关联的现有订单，所以实际上填充了以下内容`item.OrderId`:

[![](img/5f5567ab8cd156abca2b1cc9deb5ff18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h5Huuyri--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2rb3kx9lm8sg5a6j0r3o.png)

如果我们想在订单和 OrderItem 之间建立联系，但是还没有，我们可以很容易地用下面的代码来实现:

```
using(var db = new DatabaseContext()) 
{
  var order = db.Orders.SingleOrDefault();
  var item = db.OrderItems.SingleOrDefault();
  item.OrderId = order.OrderId;
  db.SaveChanges();
} 
```

### 更新

更新就像我们为订单所做的第二个创建场景一样简单。即读取一个实体，设置一个属性并调用`db.SaveChanges()`。像这样:

```
using(var db = new DatabaseContext()) 
{
  var item = db.OrderItems.SingleOrDefault();
  item.Quantity++;
  db.SaveChanges();
} 
```

### 删除

删除就像从列表中删除一样简单。如果我们想删除一个`Product`，我们只需要做以下事情:

```
using(var db = new DatabaseContext()) 
{
  var product = db.Products.SingleOrDefault();
  db.Products.Remove(product);
  db.SaveChanges();
} 
```

需要注意的是，如果您的产品是`OrderItem`的一部分，您需要首先移除该连接，如下所示:

```
using(var db = new DatabaseContext()) 
{

  var item = db.OrderItems.Include(i => i.Product)SingleOrDefault();

  item.Product = null;
  db.SaveChanges();

  var product = db.Products.SingleOrDefault();
  db.Products.Remove(product);
  db.SaveChanges();
} 
```

## 总结

我们到此为止。如果我们从绝对零度开始，我们在这篇文章中学到了很多

我们了解到:

*   什么是 ORM
*   定义我们的数据库结构
*   创建并应用迁移
*   读出数据
*   创建数据
*   更新数据
*   删除数据
*   加载相关实体
*   外键

这对于一篇文章来说太多了。希望你现在对此很感兴趣，想了解更多。如果您想学习更高级的概念以及如何处理不同类型的数据库，请查看参考资料部分。