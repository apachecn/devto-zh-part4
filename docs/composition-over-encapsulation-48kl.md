# 封装上的成分

> 原文：<https://dev.to/aleksikauppila/composition-over-encapsulation-48kl>

继承的一个大问题是它很容易失去控制，成为一个无法维护的烂摊子。它很快就变成了一棵树，向四面八方伸展。此外，当您更改一个类时，它可能会对继承对象产生意想不到的副作用。

很多时候你听到你应该更喜欢作曲而不是继承。我完全明白。它解决了遗传导致的许多问题。

但是封装呢？

```
abstract class Report
{
    abstract protected function title(): string;
    abstract protected function contents(): string;

    public function display(): string
    {
        return sprintf('%s\n%s', this.title(), this.contents());
    }
}

class SalaryReport extends Report
{
    private string title;
    private array salaries;

    public function constructor(string title, array salaries)
    {
        this.title = title;
        this.salaries = salaries;
    }

    protected function title(): string
    {
        return this.title;
    }

    protected function contents(): string
    {
        return this.salaries.join('\n');
    }
} 
```

如果我选择组合而不是继承，我将不得不放松对公共的`SalaryReport.title()`和`SalaryReport.contents()`的封装。代码会变成这样。

```
interface ReportData
{
    public function title(): string;
    public function contents(): string;
}

class Report
{
    private ReportData reportData;

    public function constructor(ReportData reportData)
    {
        this.reportData = reportData
    }
    public function display(): string
    {
        return sprintf('%s\n%s', this.reportData.title(), this.reportData.contents());
    }
}

class SalaryReportData implements ReportData
{
    private string title;
    private array salaries;

    public function constructor(string title, array salaries)
    {
        this.title = title;
        this.salaries = salaries;
    }

    public function title(): string
    {
        return this.title;
    }

    public function contents(): string
    {
        return this.salaries.join('\n');
    }
} 
```

现在`SalaryReport`不再是一个真实的对象，因为它真的没有任何值得注意的行为，它只是数据。当然，你可能会说`Report`也没做什么，但是为了这个例子，我们就用这个吧。

我怎样才能在继承的基础上使用组合，而不削弱封装性和把我的对象变成数据结构？似乎这两种方法都有严重的缺点。