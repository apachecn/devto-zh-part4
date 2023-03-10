# 在 ggplot2 中制作多点“哑铃”图

> 原文：<https://dev.to/hrbrmstr/make-multi-point-dumbbell-plots-in-ggplot2-cfm>

一个 [`{ggalt}`包](https://github.com/hrbrmstr/ggalt/)的用户最近[发了一个关于如何给`geom_dumbbell()`剧情加分的问题](https://github.com/hrbrmstr/ggalt/issues/59)。目前，这不是你可以用`geom_dumbbell()`做的事情，但是通过一点数据争论，你可以用你的数据和 ggplot2 以一种非常简单的方式做这件事。下面的例子使用每个类别 3 个值，但它应该扩展到每个类别的`n`个值(尽管在某个`n`之后，你应该重新考虑 dummbell 图的使用，以更合适的方式来可视化你试图传达的信息)。

设置如下:

```
library(hrbrthemes)
library(tidyverse)

tibble(
  val1 = c(3, 2, 4),
  val2 = c(1, 4, 5),
  val3 = c(5, 8, 6),
  cat = factor(month.name[1:3], levels = rev(month.name[1:3]))
) -> xdf 
```

Enter fullscreen mode Exit fullscreen mode

每个类别三个值。这种方法非常简单:

*   重塑数据框并获得最小值，这样你就可以画一条视线跟踪线(这是一个几何图形)
*   重塑数据框并获取最小/最大类别值，以便您可以绘制线段(这是另一个 geom)
*   重塑数据框并绘制点

我把^^的笔记放在了每张地图的旁边

```
ggplot() +
  # reshape the data frame & get min value so you can draw an eye-tracking line (this is one geom)
  geom_segment(
    data = gather(xdf, measure, val, -cat) %>% 
      group_by(cat) %>% 
      top_n(-1) %>% 
      slice(1) %>%
      ungroup(),
    aes(x = 0, xend = val, y = cat, yend = cat),
    linetype = "dotted", size = 0.5, color = "gray80"
  ) +
  # reshape the data frame & get min/max category values so you can draw the segment (this is another geom)
  geom_segment(
    data = gather(xdf, measure, val, -cat) %>% 
      group_by(cat) %>% 
      summarise(start = range(val)[1], end = range(val)[2]) %>% 
      ungroup(),
    aes(x = start, xend = end, y = cat, yend = cat),
    color = "gray80", size = 2
  ) +
  # reshape the data frame & plot the points
  geom_point(
    data = gather(xdf, measure, value, -cat),
    aes(value, cat, color = measure), 
    size = 4
  ) +
  # i just extended the scale a bit + put axis on top; choose aesthetics that work 
  # for you
  scale_x_comma(position = "top", limits = c(0, 10)) +
  scale_color_ipsum(name = "A real legend title") +
  labs(
    x = "Description of the value", y = NULL,
    title = "A good plot title"
  ) +
  theme_ipsum_rc(grid = "X") +
  theme(legend.position = "top") 
```

Enter fullscreen mode Exit fullscreen mode

这是结果:

[![](img/dc55234132c4957d6bbe93dd421ed706.png)](https://i1.wp.com/rud.is/b/wp-content/uploads/2019/06/there-are-three-points-2.png?ssl=1)