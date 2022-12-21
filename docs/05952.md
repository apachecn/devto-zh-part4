# PHP 日期和时间备忘单

> 原文：<https://dev.to/danenglishby/php-date-strtotime-cheat-sheet-with-pdf-1ni8>

如果你想把这篇文章加入书签，请点击下面的链接-[https://www.codewall.co.uk/php-date-strtotime-cheat-sheet/](https://www.codewall.co.uk/php-date-strtotime-cheat-sheet/)

如果你像我一样一直在开发一些数据丰富的项目，你会发现自己在使用`Date`函数和`Date`函数，混合使用`strtotime`、`Date` & `strtotime`是非常强大的工具，使你能够非常迅速地设置日期标准，不管是什么场景。这仅仅是关于知道如何去做，因为有许多命令你可以给这些功能，这是非常有用的。例如，“-7 天”。这篇文章将是一个在线小抄，但在文章的结尾，会有一个下载链接，里面有最流行的“秘籍”，可以完美地打印在一张 A4 纸上。您可以在此或本页末尾下载 [PDF。](https://www.codewall.co.uk/assets/pdfs/date-strtotime-cheat-sheet.pdf)

*   [当前日期&时间格式](#date-time-formatting)

    *   [4 位数年份](#4-digit-year)
    *   [两位数年份](#2-digit-year)
    *   [月份号](#month-number)
    *   [日数](#day-number)
    *   [两位数年份](#date-with-2-digit-year)
    *   [4 位数年份](#date-with-4-digit-year)
    *   [12 & 24 小时格式小时](#12-hour-amp-24-hour-format-hours)
    *   [分钟](#minutes)
    *   [秒](#seconds)
    *   [日期时间戳](#date-time-stamp)
*   [自定义日期&时间格式](#custom-date-amp-time-formatting-with-strtotime)

*   [日期&用 strtotime](#date-amp-time-manipulation-with-strtotime) 进行时间操作

    *   * [每月的第一天](#first-day-of-the-month)
        *   [每月的最后一天](#last-day-of-the-month)
        *   [从日期中减去天数](#subtract-days-from-date)
        *   [添加到日期的天数](#add-days-to-date)
        *   [从日期中减去月份](#subtract-months-from-date)
        *   [添加月份到日期](#add-months-to-date)
        *   [从日期中减去年份](#subtract-years-from-date)
        *   [添加迄今为止的年份](#add-years-to-date)
        *   [从日期中减去小时](#subtract-hours-from-date)
        *   [添加迄今为止的小时数](#add-hours-to-date)
        *   [从日期中减去分钟](#subtract-minutes-from-date)
        *   [添加到日期的分钟数](#add-minutes-to-date)
        *   [从日期中减去秒数](#subtract-seconds-from-date)
        *   [为日期添加秒数](#add-seconds-to-date)
        *   [获取昨天的日期](#date-yesterdays-date)
        *   [得到明天的日期](#date-tomorrows-date)
        *   [获取下一个日期](#get-next-date)
        *   [获取之前的日期](#get-previous-date)
        *   [次日名称](#next-day-name)
        *   [最后一天的名字](#last-day-name)
        *   [月份名称的第一天](#first-day-of-month-name)
        *   [月份名称的最后一天](#last-day-of-month-name)
        *   [中午-午夜日期](#noon-midnight-date)

### 当前日期&时间格式

让我们从简单的旧日期格式开始，有数百种不同的格式可供选择，但是您可能只会使用其中的少数几种！**请注意:**本节中的所有示例都使用**当前日期时间，**表示执行时您计算机的实时时间戳！

##### 4 位数年份(#4 位数年份)

```
 echo date('Y'); // 2019 
```

##### 2 位数年份

```
 echo date('y'); // 19 
```

##### 月数

```
 echo date('m'); // 08 
```

##### 日数

```
 echo date('d'); // 26 
```

##### 日期用 2 位数表示年份

```
 echo date('y-m-d'); // 19-08-26 
```

##### 日期用 4 位数表示年份

```
 echo date('Y-m-d'); // 2019-08-26 
```

##### 12 小时& 24 小时格式小时

**快速提示:**有两种方法可以从 date()函数中访问小时，使用以下参数可以得到相同的结果-

*   `date('g')` - (12 小时制)或`date('G')` - (24 小时制)
*   `date('h')` - (12 小时制)或`date('H')` - (24 小时制)

```
 echo date('h'); // 11 (am) (12 Hour Format Style)
    echo date('H'); // 11 (am) (24 Hour Format Style)
    echo date('g'); // 11 (am) (12 Hour Format Style)
    echo date('G'); // 11 (am) (24 Hour Format Style) 
```

##### 分钟

```
 echo date('i'); // outputs: 08 (Minutes 00-59) 
```

##### 秒

```
 echo date('s'); // outputs: 56 (Seconds 00-59) 
```

##### 日期时间戳

现在，考虑到所有这些参数，我们可以生成一个合适的日期时间戳，格式为 **YYYY-MM-DD HH:MM:SS**

```
 echo date('Y-m-d h:i:s'); // 2019-08-26 09:48:01 
```

如果我们想用另一种方式，交换年份的位置，那么格式就是 **DD-MM-YYYY HH:MM:SS**

```
 echo date('d-m-Y h:i:s'); // 2019-08-26 09:48:01 
```

### 用 strtotime 自定义日期&时间格式

在 date 函数中使用自定义日期或日期时间非常简单。所有你需要做的就是在格式化参数设置好之后用`strtotime`函数传递日期。请看下面的例子，注意，由于指定的格式，传入的日期格式是 **YYYY-MM-DD** ，而实际输出是 **DD-MM-YYYY** 。**例 1**

```
 echo date('d-m-Y h:i:s', strtotime('2018-01-31 00:00:01')); // 31-01-2018 12:00:01 
```

这种使用日期函数的精确方法将适用于本文第一部分、**示例 2** 、
中显示的所有格式

```
 echo date('Y-m-d', strtotime('2019-08-26')); // outputs 2019-08-05 
```

因此，date 函数会将传递的`strtotime`日期格式化为指定的格式参数。再举一个例子，从传入的日期中获取一个月中的某一天的数字是这样的- **例 3**

```
 echo date('d', strtotime('2019-08-26')); // outputs 05 
```

### 日期&用 strtotime 进行时间操作

老实说，`strtotime`绝对是天赐之物，能够用你实际使用的语言给它一个参数真是太棒了。例如“这个月的第一天”或“这个星期的第一天”等等。这可以在没有自定义日期的情况下使用，所以基本上是您的服务器的日期时间，或者您的规范的日期时间。在下一节中，我将演示这两个用例。

##### 每月的第一天

```
 // Using server date-time
    echo date('Y-m-d h:i:s', strtotime('first day of this month')); // 2019-08-01 19:35:00

    //Using custom date-time 
    echo date('Y-m-d h:i:s', strtotime('first day of this month', strtotime('2019-12-31 19:00:00'))); // 2019-12-01 07:00:00 
```

##### 每月的最后一天

```
 // Using server date-time
    echo date('Y-m-d h:i:s', strtotime('last day of this month')); // 2019-08-31 19:35:00

    //Using custom date-time 
    echo date('Y-m-d h:i:s', strtotime('last day of this month', strtotime('2019-12-31 19:00:00'))); // 2019-12-01 07:00:00 
```

##### 从日期减去天数

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-7 days')); // Outputs: 2019-08-19 11:43:59

    //Using custom date-time 
    echo date('Y-m-d H:i:s', strtotime('-7 days', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-06 14:00:00 
```

##### 给日期添加天数

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+7 days')); // Outputs: 2019-09-01 11:43:59

    //Using custom date-time 
    echo date('Y-m-d H:i:s', strtotime('+7 days', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-20 14:00:00 
```

##### 从日期中减去月份

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-7 months')); // Outputs: 2019-01-26 11:56:06

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('-7 months', strtotime('2019-08-13 14:00:00'))); // Outputs: 2020-03-13 14:00:00 
```

##### 添加月份日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+7 months')); // Outputs: 2020-03-26 11:56:45

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('+7 months', strtotime('2019-08-13 14:00:00'))); // Outputs: 2020-03-13 14:00:00 
```

##### 从日期中减去年份

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-5 years')); // Outputs: 2014-08-26 11:57:16

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('-5 years', strtotime('2019-08-13 14:00:00'))); // Outputs: 2014-08-13 14:00:00 
```

##### 添加年份到日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+7 years')); // Outputs: 2024-03-26 11:48:16

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('+7 years', strtotime('2019-08-13 14:00:00'))); // Outputs: 2024-03-13 14:00:00 
```

##### 从日期中减去小时

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-12 hours')); // Outputs: 2019-08-25 23:21:23

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('-12 hours', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-13 02:00:00 
```

##### 添加小时到日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+12 hours')); // Outputs: 2019-08-26 23:21:53

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('+12 hours', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-14 02:00:00 
```

##### 从日期中减去分钟

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-60 minutes')); // Outputs: 2019-08-26 10:48:23

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('-60 minutes', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-13 02:00:00 
```

##### 添加分钟日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+60 minutes')); // Outputs: 2019-08-26 12:30:33

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('+60 minutes', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-13 13:00:00 
```

##### 从日期中减去秒

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('-300 seconds')); // Outputs: 2019-08-26 11:40:33

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('-300 seconds', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-13 13:55:00 
```

##### 给日期添加秒数

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('+60 seconds')); // Outputs: 2019-08-26 11:50:58

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('+60 seconds', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-13 14:05:00 
```

##### 获取昨天的日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('yesterday')); // Outputs: 2019-08-25 00:00:00

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('yesterday', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-12 00:00:00 
```

##### 获取明天的日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('tomorrow')); // Outputs: 2019-08-27 00:00:00

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('tomorrow', strtotime('2019-08-13 14:00:00'))); // Outputs: 2019-08-14 00:00:00 
```

##### 获取下一个日期

**注**:下一个参数随**日**、**月**和**年**一起工作。

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('next month')); // Outputs: 2019-09-26 20:40:43

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('next year', strtotime('2019-08-13 14:00:00'))); // Outputs: 2020-08-13 14:00:00 
```

##### 获取以前的日期

```
 // Using server date-time
    echo date('Y-m-d H:i:s', strtotime('previous month')); // Outputs: 2019-07-26 20:40:43

    //Using custom date-time
    echo date('Y-m-d H:i:s', strtotime('previous year', strtotime('2019-08-13 14:00:00'))); // Outputs: 2018-08-13 14:00:00 
```

##### 次日名称

注意:可以使用短月份名称或完整月份名称，例如星期六或星期六

```
 echo date('Y-m-d H:i:s', strtotime('next saturday')); // Outputs: 2019-08-31 00:00:00 
```

##### 最后一天的名字

注意:可以使用短月份名称或完整月份名称，例如星期六或星期六

```
 echo date('Y-m-d H:i:s', strtotime('last saturday')); // Outputs: 2019-08-24 00:00:00 
```

##### 月名的第一天

注意:可以使用短月份名称或完整月份名称，例如二月或二月

```
 echo date('Y-m-d H:i:s', strtotime('first day of February')); // Outputs: 2019-02-01 00:00:00 
```

##### 月份名称的最后一天

注意:可以使用短月份名称或完整月份名称，例如二月或二月

```
 echo date('Y-m-d H:i:s', strtotime('last day of February')); // Outputs: 2019-02-28 00:00:00 
```

##### 正午和午夜的约会

```
 echo date('Y-m-d H:i:s', strtotime('midnight')); // Outputs: 2019-08-26 00:00:00

    echo date('Y-m-d H:i:s', strtotime('noon')); // Outputs: 2019-08-26 00:00:00 
```

**PDF 下载**请随意下载这个压缩的备忘单 PDF。你可以在这里下载[的 PDF 文件](https://www.codewall.co.uk/assets/pdfs/date-strtotime-cheat-sheet.pdf)

如果你想把这篇文章加入书签，请点击下面的链接-[https://www.codewall.co.uk/php-date-strtotime-cheat-sheet/](https://www.codewall.co.uk/php-date-strtotime-cheat-sheet/)