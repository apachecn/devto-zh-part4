# ★一个控制时间流动的包

> 原文：<https://dev.to/freekmurze/a-package-to-control-the-flow-of-time-3gi6>

想象一下，你的应用程序可以通知你的用户，但是你不想在五秒钟内发送一个以上的通知。你将如何测试时间方面？您必须创建一个需要五分钟的测试吗？

幸运的是，答案是“不”。如果你正在使用流行的 [Carbon](https://packagist.org/packages/nesbot/carbon) 库，你可以设置库认为“现在”的值。您可以使用`Carbon::setTestNow($now)`来实现，其中`$now`是`Carbon\Carbon`的一个实例。

在我的项目和包的测试套件中，我经常添加这样的代码:

```
protected function setNow(string $time, $format = 'Y-m-d H:i:s')
{
    $now = Carbon::createFromFormat($format, $time);

    Carbon::setTestNow($now);
}

protected function progressSeconds(int $seconds)
{
    $newNow = now()->addSeconds($seconds);

    Carbon::setTestNow($newNow);
}

protected function progressMinutes(int $minutes)
{
    $newNow = now()->addMinutes($minutes);

    Carbon::setTestNow($newNow);
}

protected function progressHours(int $hours)
{
    $newNow = now()->addHours($hours);

    Carbon::setTestNow($newNow);
} 
```

因为我厌倦了一遍又一遍地编写代码，我决定开发一个新的小而方便的包，名为 [spatie/test-time](https://github.com/spatie/test-time) 。使用这个包，你可以用
来冻结时间

```
// time will not progress anymore

TestTime::freeze(); 
```

或者，您可以传入一个 carbon 实例作为当前时间。

```
TestTime::freeze($carbonInstance); 
```

也不需要添加单独的功能，如`progressSeconds`、`progressMinutes`等等...你可以用任何一个碳精功能从`add`或`sub`开始计时。

```
TestTime::addMinute();

TestTime::subHours(5);

// you can also chain calls
TestTime::addMonth(3)->addYear(); 
```

下面是我在现实项目中使用它的一个例子:

> 用新发布的 spatie/test-time 包喂自己⏳[https://t.co/L0clvjrgRI](https://t.co/L0clvjrgRI)[# PHP](https://twitter.com/hashtag/php?src=hash&ref_src=twsrc%5Etfw)[#测试](https://twitter.com/hashtag/testing?src=hash&ref_src=twsrc%5Etfw)[pic.twitter.com/xkDFaXpTsf](https://t.co/xkDFaXpTsf)
> 
> -小鹿的弗里克？(【t0)@自由石匠】[2012 年 6 月 12 日，2019 年](https://twitter.com/freekmurze/status/1138879576023674881?ref_src=twsrc%5Etfw)

快乐的摆弄时间！