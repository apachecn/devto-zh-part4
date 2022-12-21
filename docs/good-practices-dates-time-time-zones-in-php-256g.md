# 良好实践:PHP 中的日期、时间和时区

> 原文：<https://dev.to/anastasionico/good-practices-dates-time-time-zones-in-php-256g>

你怎么知道早上什么时候该睡觉或者醒来？

如果你是一个网络开发人员，这个问题可能很简单，

如果你开始睡着了，去喝杯咖啡，然后继续写代码。

我也做过，

这可能行得通，但是过一会儿，我们的生产力水平就会下降，这可能会导致错误，甚至更糟:导致糟糕的代码。

我们的身体，为了防止我们写下第二天早上无法理解的恐惧或被项目经理解雇，已经在我们的眼睛后部开发了一些极其特殊的细胞，称为*光敏视网膜神经节细胞*。

[![](img/881b26576e77e94d5692e02f2cb129ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iro6xebm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anastasionico.uk/img/1/beautiful-eyes-blue-eyes-1564185.jpg)

这些细胞负责计算我们从外部世界接收的亮度，并将我们内部生物钟的状态从清醒切换到昏昏欲睡。

不幸的是，我们的 pc 或 MAC 就没有这么幸运了，

是的，他们有一个内部时钟，但它的效率甚至不接近我们的，对于一个 PHP 开发人员来说，这是一个问题，直到几年前。

现在一切都变了！

在 PHP 中管理日期和时间仍然是一件痛苦事情，但是从 PHP 5.2 开始已经变得越来越容易了。

在这篇文章中，你将会学到所有让你在 PHP web 应用程序上管理时间的类

## 

## 级数

在本系列中，我们将探索 web 开发人员在创建或管理 PHP 代码时必须注意的最佳实践。

[整理、验证和转义](http://anastasionico.uk/blog/good-practices-how-to-sanitize-validate-and-escape-in-php)
[安全和管理密码](http://anastasionico.uk/blog/good-practices-php-security-manage-password)
[处理错误和异常](http://anastasionico.uk/blog/good-practices-handling-error-and-exceptions-in-php)
[日期和时间](http://anastasionico.uk/blog/good-practices-date-time-time-zones-php)

## 

## 服务器时区

当你开始一个新的网络应用程序时，你的清单上的首要任务之一就是**设置正确的时区**。

通过声明它，你将避免数据库的错误和烦人的警告信息。

有两种不同的方法来设置时区。

我建议的第一个方法是在 php.ini 文件中声明时区。

使用以下命令

*日期.时区= '欧洲/伦敦'*

您还可以在运行时声明默认时区。

在这种情况下，您需要使用如下 PHP 命令:

```
date_default_timezone_set("Europe/London"); 
```

Enter fullscreen mode Exit fullscreen mode

当然，伦敦只是一个例子，你需要用你喜欢的那个。

在 PHP 手册中，你可以找到支持时区的完整列表。

## 

## 日期时间类

在 PHP 5.2 中，一场小小的革命开始了，

创建了 *DateTime* 类，处理日期不再是不可能完成的任务。

它实际上变得非常容易使用，功能的数量令人印象深刻。

让我们先来看一下技术，然后你会看到一些例子。

**DateTime 类替换了内置函数 date()和 Time()。**

这两个函数仍然可用，但是最好使用类并使用日期对象。

*DateTime* 实现了*DateTime 接口。*

这个接口提供了几个常量和几个方法，比如返回两个日期之差的 *DateTime::diff()* ，或者返回给定格式的日期的 *DateTime::format()* 。

要创建一个新的 *DateTime* 对象，您需要传递一个字符串格式的变量，其中包含您想要创建的日期，然后将解析该字符串并创建对象。

你可以使用许多不同的格式来创建一个对象，从 UNIX 到伪代码，到美国日期到普通英语。

看看下面这些例子:

```
$stringsOfDate = ['Next Monday', '', '1 January 2019', '+1 week'];
foreach($stringsOfDate as $stringOfDate) {
    $dateTime = new DateTime($stringOfDate);
    echo $dateTime->format(DateTime::RSS) . PHP_EOL;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，我们创建并输出了 4 个 DateTime 对象，以 4 种不同的格式注入参数。

第一个字符串是用普通英语写的，将显示下周一的日期，传递的第二个参数是一个空字符串，PHP 在这种情况下不会返回错误，它将返回当前时间戳，然后我们得到一个用欧洲格式写的日期，最终是一些伪代码。

它们都将工作，并以 RSS 格式显示，如下所示:

*D，d M Y H:i:s O*

### 

### 常量

片段中的常量 RSS 和上面奇怪的字母序列代表了日期实际上是如何显示的

让我们从格式化代码开始:

*   **Y** 代表一个完整的四位数年份(2019)
*   这是一个两位数的月份(03)
*   **d** 一个月中的某一天，两位数，带前导零(06)
*   **D** 三个字母考证的日子(星期二)
*   **H** 带前导零的 24 小时格式(03)
*   **i** 带前导零的两位数分钟(59)
*   **s** 带前导零的两位数秒(59)
*   **O** 以小时为单位的 GMT 时差
*   **T** 时区缩写(EST)

还有很多类似的东西，你可以在官方的 PHP 手册中找到。

既然您已经理解了这些字母是如何工作的，那么让我们来看看 DateTimeInterface 中可用的常量，也就是 DateTime 类中的常量。

*   日期时间接口::原子 Y-m-d\TH:i:sP
*   DateTimeInterface::COOKIE l，d-M-Y H:i:s T
*   日期时间界面::ISO8601 Y-m-d\TH:i:所以
*   DateTimeInterface::RFC822 D，d M y H:i:s O
*   日期时间接口::RFC850 l，d-M-y H:i:s T
*   日期时间接口::RFC1036 D，d M y H:i:s O
*   日期时间接口::RFC1123 D，d M Y H:i:s O
*   datetime interface::RFC 2822D，d M Y H:i:s O
*   日期时间接口::RFC3339 Y-m-d\TH:i:sP
*   datetime interface::RFC 3339 _ EXTENDEDY-m-d \ TH:I:s . VP
*   DateTimeInterface::RSS D，d M Y H:i:s O
*   日期时间接口::W3C Y-m-d\TH:i:sP

### 

### 日期计算

在玩日期游戏时，有几种方法可以进行计算，

在这一段，我会告诉你一个快速的和适当的。

进行计算的最快方法是使用 DateTime 类的 modify()方法。

它需要一个字符串作为参数，并返回更新的日期时间对象

```
// Creating a new DateTime object using ‘now’ as date
$dateTime = new DateTime();

// adding one month to current date and output the result
$dateTime->modify('+1 month');

echo $dateTime->format(DateTime::COOKIE) . PHP_EOL; 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法，也是 web 开发专家认为的正确的计算方法，是使用*DateTime interval*类和 *DateTime add()* 和 *sub()* 方法。

为了实例化一个 *DateInterval* 对象，您需要了解 interval_spec。

它代表区间规范，只是一个奇怪的字符串，需要一个非常特殊的格式。

这个字符串以字母 *P* 开头，代表“周期”,然后一对数字后面跟着周期指示符，它们从最大到最小排序。

此外，如果您想要指定时间，您需要在它前面加上字母 *T* ，它代表“时间”。

很困惑，不是吗？

我们一起造几个吧。

假设我们要向日期时间对象添加 1 个月、2 周、3 天。

在另一个示例中，我们希望将 1 年 1 小时 1 分 30 秒删除到日期时间对象中。

1.  我们开始添加句号“P”
2.  然后添加 1 个月“P1M”
3.  然后增加 2 周' P1M2W '
4.  然后添加 3 天' P1M2W3D '

这是我们需要的第一根绳子，

让我们用时间做第二个

1.  我们开始添加句号“P”
2.  然后加上 1 个月“P1Y”
3.  现在我们需要时间，让我们添加 T 前缀' P1YT '
4.  然后添加 1 小时' P1YT1H '
5.  然后添加 1 分钟“P1YT1H1M”
6.  然后添加 30 秒“P1YT1H1M30S”

现在我们有了在创建 DateInterval 对象
时必须用作参数的两个字符串

```
$dateTime = DateTime::createFromFormat('d-m-Y H:i:s', "27-09-2019 20:45:30");
// the current $datetime value is 27-09-2019 20:45:30

$intervalToAdd = new DateInterval('P1M2W3D');
$dateTime->add($intervalToAdd);
// adding $intervalToAdd to $dateTime the current $datetime value is 27-10-2019 23:45:30
echo $dateTime->format(DateTime::COOKIE) . PHP_EOL;
// this block of code will output: Wednesday, 30-Oct-2019 20:45:30 UTC

$intervalToSubtract = new DateInterval('P1YT1H1M30S');
$dateTime->sub($intervalToSubtract);
// subtracting $intervalToSubtract to $dateTime the current $datetime value is 27-10-2019 23:45:30
echo $dateTime->format(DateTime::COOKIE) . PHP_EOL;
// this block of code will output: Tuesday, 30-Oct-2018 19:44:00 UTC 
```

Enter fullscreen mode Exit fullscreen mode

### 手工计算

#### 什么是 Unix 纪元？

有时您只想自己做一些计算，而不使用 DateTime 类提供的所有这些花哨的方法。

PHP 提供了一些函数，可以让你创建日期，根本不需要你调用类，甚至不需要你使用 OOP。

这些 PHP 函数利用了 UNIX 时间戳。

**Unix 时间戳是一个不断增加的数字，用于计算自 Unix 纪元(1970 年 1 月 1 日 00:00:00 GMT)以来经过的秒数。**

使用这种方法的优点是不受时区限制，并且很容易在代码中实现。

这两个 PHP 函数是:

#### str toime()

**该函数解析英文文本日期时间描述，并返回一个 Unix 时间戳**

你可以用你喜欢的每一句话作为参数，它会被接受。

你不相信我？

下面有几个直接来自手册的例子:

```
strtotime("now");
// 1569396041
strtotime("10 September 2000");
// 968544000
strtotime("+1 day");
// 1569482441
strtotime("+1 week");
// 1570000841
strtotime("+1 week 2 days 4 hours 2 seconds");
// 1570188043
strtotime("next Thursday");
// 1569456000
strtotime("last Monday");
// 1569196800 
```

Enter fullscreen mode Exit fullscreen mode

这非常容易使用，现在你可以随心所欲地操作和处理这些整数。

#### mktime()

**“make-time”函数因其参数而有点复杂，但没那么复杂，**

它返回一个长整数形式的 Unix 时间戳，对应于给定的参数。

你不需要插入所有的参数，事实上，你根本不需要插入任何参数，因为这个函数要求参数以特定的顺序插入，PHP 会考虑当前的顺序。

这里有几个例子

```
// mktime(h, i, s, m, d, y)

date('F jS, Y g:i:s a', mktime(0, 0, 0, 0, 0, 2013));
// November 30th, 2012 12:00:00 am
date('F jS, Y g:i:s a', mktime(1, 1, 1, 1, 1, 2013));
// January 1st, 2013 1:01:01 am
date("M-d-Y", mktime(0, 0, 0, 12, 32, 1997));
// Jan-01-1998 
date("M-d-Y", mktime(0, 0, 0, 13, 1, 1997));
// Jan-01-1998 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意的话，你一定会注意到上面结果中的一些奇怪之处，

尽管我们指定了另一个日期，但最后两个示例的结果是 1998 年 1 月的第一天，

这样做的原因是，如果您传递的参数大于特定位置允许的值，PHP 足够聪明，可以理解它并引用下一个句点。

在这种情况下，一年中没有 12 月 32 日或 13 月，因此 PHP 将该值视为下一年 1 月的第一天。

这不是很聪明吗？

### 如何比较日期？

*DateTime* 的另一个伟大功能是计算两个日期之间的差异，

您可以对一个*日期时间*对象使用 *diff* ()函数，并将另一个*日期时间*对象作为参数传递，您将拥有一个*日期间隔*类(见下文),表示两个对象之间的时间段

谈话到此结束，让我们看看代码。

```
$now = new DateTime();
$christmas = new DateTime('25 December');
if ($now > $christmas) {
  $christmas = new DateTime('25 December next year');
}

$interval = $christmas->diff($now);

echo "$interval->days days until Christmas";
// 90 days until Christmas 
```

Enter fullscreen mode Exit fullscreen mode

*$interval* 是*datetime interval*类的一个对象实例，它的方法 *days()* 返回一个整数，显示从现在到圣诞节的天数，我写这个句子的时候是 90。

最好快点拿礼物！

## 

## 日期间隔类

我们刚刚在本文的前一部分看到了如何使用*日期间隔*的示例，

现在我们将深入这个类本身。

DateInterval 可以表示两个时间长度，一个固定的时间长度(例如:一周)或一个相对的时间长度(例如:昨天)。

我们使用*日期间隔*的原因是为了修改日期时间实例。

我们在上面看到了如何使用 *add()* 和 *sub()* 方法来操作日期。

Dateinterval 有一个构造函数方法，

如果你不知道什么是构造函数方法，你可以在面向对象的基础系列中阅读

正如我们之前看到的，这个构造需要一个间隔规范，或者，您可以使用 createFromDateString()函数，该函数接受一个简单的字符串

```
$objConstructed = new DateInterval('P1DT12H');
$objFromDateString = DateInterval::createFromDateString('1 day + 12 hours');
object(DateInterval)#1 (16) {
  ["y"]=>
  int(0)
  ["m"]=>
  int(0)
  ["d"]=>
  int(1)
  ["h"]=>
  int(12)
  ["i"]=>
  int(0)
  ["s"]=>
  int(0)
  ["f"]=>
  float(0)
  ["weekday"]=>
  int(0)
  ["weekday_behavior"]=>
  int(0)
  ["first_last_day_of"]=>
  int(0)
  ["invert"]=>
  int(0)
  ["days"]=>
  bool(false)
  ["special_type"]=>
  int(0)
  ["special_amount"]=>
  int(0)
  ["have_weekday_relative"]=>
  int(0)
  ["have_special_relative"]=>
  int(0)
}
// Both ways return an identical DateInterval object 
```

Enter fullscreen mode Exit fullscreen mode

请注意，*日期间隔*不支持分割秒(微秒或毫秒等)。)

## 

## date time zone 类

如果你和来自不同国家的客户一起工作过，你就会知道在不同的时区工作通常是痛苦的来源。

您在数据库中保存了一些东西，但时间戳并不是您想要的，

您运行一个 MySql 查询，其中修改的字段有一个特定的时间段，这一切都搞砸了，只是因为您或用户在不同的时区。

**PHP 解决这个问题有几种方法，其中一种是 DateTimeZone 类的用户，**

创建一个 *DateTimeZone* 对象是非常容易的，你唯一需要的值是一个代表一个支持的时区字符串的字符串。

在我看来，

这种类型的对象最常见的用途是在创建一个新的 *DateTime* 实例时，该构造的第二个参数实际上是

DateTimeZone 类，它不是强制的，但建议添加它，尤其是在多语言 web 应用程序中。

```
$timezone = new DateTimeZone('Europe/London');
$datetime = new DateTime('2019-10-01', $timezone);
object(DateTime){
  ["date"]=>
  string(26) "2019-10-01 00:00:00.000000"
  ["timezone_type"]=>
  int(3)
  ["timezone"]=>
  string(13) "Europe/London"
}

$datetime->setTimezone(new DateTimeZone('Asia/Dhaka'));
object(DateTime){
  ["date"]=>
  string(26) "2019-10-01 05:00:00.000000"
  ["timezone_type"]=>
  int(3)
  ["timezone"]=>
  string(10) "Asia/Dhaka"
} 
```

Enter fullscreen mode Exit fullscreen mode

我个人认为，当我知道国外的用户将要连接到我开发的一个网站时，不要发疯。

我将时区设置为服务器的时区，所以每个数据都是一致的。

如果需要，我可以使用本文中的一个函数来更改日期。

## 

## 日期周期类

你有没有忘记过你父母的生日，更糟糕的是你和女朋友的纪念日？

别担心，PHP 会帮你搞定。

DatePeriod 是负责处理重复事件和日期的 PHP 类，

**有 3 种不同的方法来创建日期周期类，**

第一个方法是传递一个 *ISO 8601* 字符串，这是一个描述周期间隔的字符串。
T3】

[您可以在这里在线创建 ISO sting】](https://www.infobyip.com/epochtimeconverter.php)

当您知道希望代码进行的总迭代次数时，您需要使用第二种方法。

该构造需要 3 个参数，第四个不是必需的

这些参数是您希望开始迭代的日期、您希望用于在日期之间迭代的时间间隔、您希望在结束之前执行的迭代次数，最后，如果您希望从第二次迭代开始，您可以键入标志 DatePeriod::EXCLUDE_START_DATE

第三种方法与第二种方法非常相似，唯一的区别是您定义了结束日期，而不是定义您想要的周期数。

以下是这三种方法的示例

```
echo "Constructor using Iso";
$iso = 'R8/2019-10-31T00:00:00Z/P7D';  
$periodISO = new DatePeriod($iso);
foreach ($periodISO as $date) {
    echo $date->format('Y-m-d');
}

echo "Constructor using defined recurrences";
$start = new DateTime('2019-10-31');
$interval = new DateInterval('P7D');
$recurrences = 8;  
$periodRecurrences = new DatePeriod($start, $interval, $recurrences);
foreach ($periodRecurrences as $date) {
    echo $date->format('Y-m-d');
}

echo "Constructor using end date";
$start = new DateTime('2019-10-31');
$interval = new DateInterval('P7D');
$end = new DateTime('2019-12-31');
$periodEnd = new DatePeriod($start, $interval, $end);
foreach ($periodEnd as $date) {
    echo $date->format('Y-m-d');
} 
```

Enter fullscreen mode Exit fullscreen mode

## nes bot/碳

如果你在你的 PHP 应用程序中使用日期和时间，并且你知道如何使用 Composer，你必须使用 Brian Nesbitt 组件 Carbon。

它非常容易使用，有非常详细的文档和许多功能，几乎在每个方面都改进了 DateTime 类。

对此我没什么可说的了，

看看下面的片段

```
$howOldAmI = Carbon::createFromDate(1975, 5, 21)->age;

Carbon::now()->subMinutes(2)->diffForHumans(); // '2 minutes ago'

if (Carbon::now()->isWeekend()) { echo 'Party!';}

$date = Carbon::now()->locale('it_IT');
echo $date->locale();            // it_IT
echo $date->diffForHumans();     // 1 secondo fa
echo $date->monthName;           // settembre
echo $date->isoFormat('LLLL');   // giovedì 26 settembre 2019 09:28 
```

Enter fullscreen mode Exit fullscreen mode

这真是太神奇了。

[这是 Nesbot Carbon 的完整文档](https://carbon.nesbot.com/docs/)

如果你发现了新的东西，学习更多就像点击下面的图片一样简单
[![http___eepurl.com_dIZqjf.jpg](img/bb27801989cd5a89bc2143e56c4d2f84.png)](http://eepurl.com/dIZqjf)

## 结论

正如你在这篇文章中发现的那样，运行我们的 web 应用程序的计算机在它们的 CPU 后面没有惊人的单元，这些单元根据我们的访问者登录的小时或他所在的时区自动切换。

毫无疑问，在 web 应用程序中管理日期、时间和时区是一项艰巨的任务。

然而，

我们现在(希望)都在使用 PHP 7 和这种编程语言的更高版本，所以没有借口不实现你刚刚在这里读到的特性。

这些好的实践实际上会让你的工作变得更加容易，让你的软件更加可伸缩和可靠。