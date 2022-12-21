# PHP 通过周数获得一周的第一天和最后一天

> 原文：<https://dev.to/jeremysawesome/php-get-first-and-last-day-of-week-by-week-number-3pcd>

我在这里添加了一个简单的帖子，用一个 PHP 方法帮助了我。此方法根据给定的年份和周数计算一周的开始和结束。我遇到的问题是“一周的第一天”是主观的。一些人认为一周的第一天是“星期一”，而另一些人认为一周的第一天是“星期日”。ISO-8601 将一周的第一天指定为“星期一”。然而，大多数西方日历显示星期日是一周的第一天，星期六是一周的最后一天。

更令人困惑的是，PHP 的方法本身似乎对一周的第一天和最后一天感到困惑。

例如:

```
$new_date = new DateTime;
// returns Monday, Jan 29 2018
$new_date->setISODate(2018, 5);

// returns Sunday, Feb 4 2018
$new_date->modify('sunday this week');

// returns Sunday, Jan 28 2018
$new_date->setISODate(2018, 5 ,0); 
```

您会注意到，字符串“本周星期日”实际上返回的是 2 月 4 日星期日，而将日期设置为同一周的第 0 天返回的是 1 月 28 日星期日。我不是说星期天不是一周两次…但是星期天不是一周两次。

总而言之，下面的方法是我发现的返回最有帮助的结果的方法:

```
function get_first_and_last_day_of_week( $year_number, $week_number ) {
    // we need to specify 'today' otherwise datetime constructor uses 'now' which includes current time
    $today = new DateTime( 'today' );

    return (object) [
        'first_day' => clone $today->setISODate( $year_number, $week_number, 0 ),
        'last_day'  => clone $today->setISODate( $year_number, $week_number, 6 )
    ];
} 
```

> 从[https://jeremysawesome . com/2019/08/12/PHP-get-first-and-last-day of-week-by-week-number/](https://jeremysawesome.com/2019/08/12/php-get-first-and-last-day-of-week-by-week-number/)交叉过账