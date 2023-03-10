# LINQ 收藏和外国收藏

> 原文：<https://dev.to/bradwellsb/linq-collections-and-foreach-4e3g>

[来源](https://wellsb.com/csharp/beginners/csharp-list-foreach/)

在本教程中，您将学习如何对列表和其他 LINQ 集合使用 ForEach 方法。这允许您在一行代码中简洁地编写至少需要四行代码的内容。

## 初始设置

在[之前的项目](https://wellsb.com/csharp/beginners/linq-syntax-filter-list/)中，我们首先创建了一个定制的 Book 类，用于保存书籍的标题、作者和出版日期。然后，我们初始化了一个包含十本书的 List < Book >变量。接下来，我们过滤列表并执行一些排序操作。最后，我们将新列表打印到控制台上。上一教程末尾的代码可能看起来像下面这样。我们将以此作为本教程的基线。

```
using System;
using System.Linq;
using System.Collections.Generic;

namespace IntroducingLINQ
{
    class Program
    {
        static void Main(string[] args)
        {
            List<Book> bookList = new List<Book>()
            {
                new Book("Les Miserables", "Victor Hugo", 1862),
                new Book("L'Etranger", "Albert Camus", 1942),
                new Book("Le Tour du monde en quatre-vingts jours", "Jules Verne", 1869),
                new Book("Madame Bovary", "Gustave Flaubert", 1857),
                new Book("Le Comte de Monte-Cristo", "Alexandre Dumas", 1844),
                new Book("Vingt mille lieues sous les mers", "Jules Verne", 1872),
                new Book("Les Trois Mousquetaires", "Alexandre Dumas", 1844),
                new Book("Candide", "Voltaire", 1759),
                new Book("Notre-Dame de Paris", "Victor Hugo", 1831),
                new Book("Voyage au centre de la Terre", "Jules Verne", 1864)
            };

            var subList = bookList.Where(p => p.Author == "Jules Verne" || p.Author == "Victor Hugo")
                .OrderBy(p => p.Author)
                .ThenBy(p => p.PubDate);

            Console.WriteLine($"{subList.Count()} books match your search criteria:");

            foreach (Book book in subList)
            {
                Console.WriteLine($" {book.Author} - {book.Title} ({book.PubDate})");
            }

            Console.ReadLine();
        }
    }

    class Book
    {
        public string Title { get; set; }
        public string Author { get; set; }
        public int PubDate { get; set; }

        public Book(string title, string author, int pubDate)
        {
            Title = title;
            Author = author;
            PubDate = pubDate;
        }
    }
} 
```

## IEnumerable 和 ToList()

当我们在第 25 行使用`var`关键字时，编译器返回一个 IOrderedEnumerable 类型。当对一组数据执行顺序操作时，这是一种有效的数据类型。例如，如果集合将被迭代或重新排序多次，则不需要修改原始源列表。在大多数情况下，建议保留返回的数据类型。

但是，有些操作可以在列表上执行，但不能在 IEnumerable 数据类型上执行。在本教程中，我们将执行一个这样的操作。幸运的是，有一种方法可以使用`ToList()`方法将 IOrderedEnumerable 或 IEnumerable 转换为列表。

您需要修改第 25-27 行。首先，将数据类型显式定义为`Book<List>`。然后，将`.ToList()`方法链接到操作的末尾。这将把 IEnumerable 类型转换成列表< T >。

```
List<Book> subList = bookList.Where(p => p.Author == "Jules Verne" || p.Author == "Victor Hugo")
    .OrderBy(p => p.Author)
    .ThenBy(p => p.PubDate)
    .ToList(); 
```

## LINQ 收藏与外国收藏

现在，我们可以替换初始设置代码第 31-34 行的`foreach`语句。

```
subList.ForEach(p => Console.WriteLine($" {p.Author} - {p.Title} ({p.PubDate})")); 
```

这一行告诉我们，对于列表中的每一项，将与该项对应的一些信息写入控制台。这个例子很简单，与操作列表中的实际数据没有任何关系。但是，也可以调用集合的`ForEach()`方法来执行许多其他有用的操作。

例如，假设我们决定向 Book 类添加一个新属性——一本书的销售价格。

```
public double SalePrice { get; set; } 
```

不用在实例化时设置 SalePrice 的值，我们可以使用集合的`ForEach()`方法给所有的书分配一个初始价格。

```
bookList.ForEach(p => p.SalePrice = 14.99); 
```

现在，假设你的书店正在对所有儒勒·凡尔纳的书进行 2 欧元的特价销售。在您了解 LINQ 之前，您可能使用过以下代码。

```
foreach (Book book in bookList)
{
    if (book.Author == &quot; Jules Verne&quot;)
    {
        book.SalePrice -= 2;
    }
} 
```

然而，使用 LINQ，所有这些代码行都可以用一行代码来代替，只需将一个`ForEach()`循环语句和一个`Where()`过滤方法链接在一起。

```
bookList.Where(p => p.Author == "Jules Verne").ToList().ForEach(p => p.SalePrice -= 2) 
```

您最终的项目代码可能类似于以下代码:

```
using System;
using System.Linq;
using System.Collections.Generic;

namespace IntroducingLINQ
{
    class Program
    {
        static void Main(string[] args)
        {
            List<Book> bookList = new List<Book>()
            {
                new Book("Les Miserables", "Victor Hugo", 1862),
                new Book("L'Etranger", "Albert Camus", 1942),
                new Book("Le Tour du monde en quatre-vingts jours", "Jules Verne", 1869),
                new Book("Madame Bovary", "Gustave Flaubert", 1857),
                new Book("Le Comte de Monte-Cristo", "Alexandre Dumas", 1844),
                new Book("Vingt mille lieues sous les mers", "Jules Verne", 1872),
                new Book("Les Trois Mousquetaires", "Alexandre Dumas", 1844),
                new Book("Candide", "Voltaire", 1759),
                new Book("Notre-Dame de Paris", "Victor Hugo", 1831),
                new Book("Voyage au centre de la Terre", "Jules Verne", 1864)
            };

            bookList.ForEach(p => p.SalePrice = 14.99);

            bookList.Where(p => p.Author == "Jules Verne").ToList().ForEach(p => p.SalePrice -= 2);

            List<Book> orderedList = bookList.OrderBy(p => p.Author).ThenBy(p => p.PubDate).ToList();

            orderedList.ForEach(p => Console.WriteLine($"{p.SalePrice:C} - {p.Author} - {p.Title} ({p.PubDate})"));

            Console.ReadLine();
        }
    }

    class Book
    {
        public string Title { get; set; }
        public string Author { get; set; }
        public int PubDate { get; set; }
        public double SalePrice { get; set; }

        public Book(string title, string author, int pubDate)
        {
            Title = title;
            Author = author;
            PubDate = pubDate;
            //SalePrice = 14.99;
        }
    }
} 
```

## 底线

在本教程中，您学习了如何将通用 IEnumerable 类型 IEnumerable <t>转换为通用列表 List <t>。请记住，只有当您需要执行某些无法在 IEnumerable 上执行的操作时，才需要这样做。您还学习了如何在 C#列表中使用`ForEach()`方法。这允许您在一行中简洁地编写原本需要多行代码的内容。LINQ 还有很多值得探索的地方，所以如果你有问题，请在评论中告诉我！</t></t>

[来源](https://wellsb.com/csharp/beginners/csharp-list-foreach/)