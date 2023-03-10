# cs 101-Python 练习:计算两个日期之间的天数

> 原文：<https://dev.to/yairmorr/cs101-python-exercise-counting-days-between-two-dates-3jh>

### CS101 — Python 练习:计算两个日期之间的天数

[![](img/d6b30fec1de829e6e0682a10ca9690d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ThRBjtjX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ax8me-NapICbyazKHYvhX9w.jpeg)

作为我正在上的 uda city[计算机科学在线入门课程](https://eu.udacity.com/course/intro-to-computer-science--cs101)的一部分，我遇到了这个有趣的练习。目标是创建一个函数，将两个日期作为输入，并计算它们之间的天数。第一次约会总是比第二次早。我的解决方案可能不是最优雅的，我的代码可以更短更有效。然而，理解逻辑是很重要的，这也是我想在这篇文章中关注的。这个练习是用 Python 写的。

如果我们把两个输入日期放在一个时间线上，算法是从外向内工作的。首先，它计算时间线每一端的月份中的天数，然后是每年每个月的天数，最后是中间所有年份的天数。两个输入日期之间的距离越长，算法正确计算日期所需的条件就越多。

#### 第一步——统计每个月的天数