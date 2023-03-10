# 用自定义函数隐藏复杂性:计算热度指数

> 原文：<https://dev.to/davidgs/hiding-complexity-with-custom-functions-calculating-heat-index-3o6j>

你有没有看过当地的天气预报——或者天气频道——听他们宣布温度，然后是“热指数”？或者外面**感觉**有多热？至少在美国，尤其是在南方，这是夏季天气预报的一部分。但是你有没有想过 T2 是如何计算炎热指数的？由于我收集温度和湿度数据，我决定有一天我要去弄清楚。毕竟，这能有多难呢？提示:永远不要问自己这个问题！

## 热度指数计算

当我去寻找我确信是一个简单的计算，将温度和湿度读数转换成热指数(HI)时，我很抱歉我走上了这条路。我轻而易举地找到了美国国家海洋和大气管理局(NOAA)列出公式的一页。这是一个叫做罗斯富兹回归方程的东西，它是这样的:

**HI =-42.379+2.04901523 * T+10.14333127 * RH-. 22475541 * T * RH-. 00683783 * T-. 05481717 * RH * RH+. 00122874 * T * T * RH+. 00085282 * T * RH * RH * RH-. 00000199 * T ***

其中 **T** 为温度， **RH** 为相对湿度。简单吧？没那么快！事实证明，这个(简单的)等式有一大堆警告。例如，如果相对湿度小于 13%,温度在 80°F 到 112°F 之间，那么您必须通过从热指数中减去下面的**调整**来调整热指数:

**调整= [(13-RH)/4]*SQRT{[17-ABS(T-95。)]/17}**

好吧，好吧。但是等等，还有呢！如果相对湿度在 85%以上，温度在 80°F 到 112°F 之间，那么你需要**增加**以下的**调整**:

**调整= [(RH-85)/10] * [(87-T)/5]**

杰出！我们终于到了…等等，不，我们还没到。如果热指数低于 80°F，那么您就不需要使用该计算来确定热指数。你可以用一个更简单的斯蒂德曼公式:

**HI = 0.5 * { T+61.0+[(T-68.0)* 1.2]+(RH * 0.094)}**

所以，首先你要用那个“简单”的方程计算热指数——根据 NOAA 的说法，你要用温度读数求平均值。如果温度低于 80 华氏度，你就有答案了。如果不是，你需要继续进行 Rothfusz 回归，并根据需要增加(或减少)任何调整。

看到了吗？很简单，真的。

## 做着变化不定的事

这就是一些复杂的通量发挥作用的地方。你看，我的温度和湿度读数是不同的测量值，所以首先要做的是建立表格，并把它们连接起来(流动的交叉测量数学之美太棒了！).

```
humidity = from(bucket: "telegraf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r._measurement == "environment" and (r._field == "humidity"))
  |> aggregateWindow(every: 30s, fn: mean)
  |> fill(column: "_value", usePrevious: true)
  |> keep(columns: ["_value", "_time"])

temperature = from(bucket: "telegraf")
  |> range(start: v.timeRangeStart)
  |> filter(fn: (r) => r._measurement == "temperature" and (r._field == "temp_f"))
  |> aggregateWindow(every: 30s, fn: mean)
  |> fill(column: "_value", usePrevious: true)
  |> keep(columns: ["_value", "_time"])
  |> yield(name: "Temperature") 
```

Enter fullscreen mode Exit fullscreen mode

这给了我两个值表。(附注:我提供了温度表，这样我的图表将在同一个图表上显示温度和热指数。)

接下来，我连接两个表:

```
first_join = join(tables: {temperature: temperature, humidity: humidity}, on: ["_time"])
  |>map(fn: (r) => ({temperature: r._value_temperature, humidity:r._value_humidity, _time: r._time}))
  |> keep(columns: ["_time", "humidity", "temperature"]) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我有了一个单独的表，按时间排列，列为`temperature`和`humidity`。剩下的就是做计算了:

```
|> map(fn: (r) => ({t: r.temperature, h: r.humidity}))
  |> map(fn: (r) => ({
     r with heatIndex:
       if ((0.5 * (r.t + 61.0 + ((r.t-68.0)*1.2) + (r.h*0.094)))/2.0) < 80.0 then (0.5 * (r.t + 61.0 + ((r.t - 68.0)*1.2) + (r.h*0.094)))
       else if ( r.h < 13.0 and r.t > 80.0) then ((-42.379 + 2.04901523*r.t + 10.14333127*r.h - .22475541*r.t*r.h - .00683783*r.t*r.h - .05481717*r.t*r.h + .00122874*r.t*r.t*r.h + .00085282*r.t*r.h*r.h - .00000199*r.t*r.t*r.h*r.h - (((13.0-r.h)/4.0)*math.sqrt(x: ((17.0-math.abs(x: (r.t-95.0))/17.0))))))
        else if r.h > 85.0 and r.t >= 80.0 and r.t <= 87.0 then ((-42.379 + 2.04901523*r.t + 10.14333127*r.h - .22475541*r.t*r.h - .00683783*r.t*r.h - .05481717*r.t*r.h + .00122874*r.t*r.t*r.h + .00085282*r.t*r.h*r.h - .00000199*r.t*r.t*r.h*r.h) + (( r.h-85.0 )/10.0) *((87.0-r.t)/5.0))
        else (-42.379 + 2.04901523*r.t + 10.14333127*r.h - .22475541*r.t*r.h - .00683783*r.t*r.h - .05481717*r.t*r.h + .00122874*r.t*r.t*r.h + .00085282*r.t*r.h*r.h - .00000199*r.t*r.t*r.h*r.h)
      })
  )
  |> map(fn: (r) => ({_value: r.heatIndex, _time: r._time}))
  |> yield(name: "HeatIndex") 
```

Enter fullscreen mode Exit fullscreen mode

我告诉过你这是复杂的流动！

但是最后，您可以在同一个表格单元格中显示实际温度和计算的热指数:

[![](img/a9ac6fe88c30dd583ba4fde3e8d4a8d5.png)](https://www.influxdata.com/wp-content/uploads/Screen-Shot-2019-08-22-at-9.14.44-AM.png) 这真是一件相当酷的事情！

## 下一步

因为有很多复杂的流动。下一步是将所有这些都纳入 Flux 函数，这样您就可以简单地调用带有`temperature`和`humidity`列的表中的`|> HeatIndex()`，并返回正确的表，其中包含所有为您计算的结果热指数。因此，我正在将`HeatIndex()`函数添加到通量中，这样当你计算热指数时，你就可以得到非常好的通量。

我打算加入理想气体定律(一个简单的)和风寒(另一个丑陋的)的计算。)以及从 F 到 C 的转换器，所以如果您对用于环境计算的东西有任何其他想法，请[联系](https://twitter.com/intent/follow?screen_name=davidgsIoT)让我知道！

用自定义函数隐藏复杂性的帖子[:计算热度指数](https://davidgs.com/2019/hiding-complexity-with-custom-functions-calculating-heat-index/)最早出现在[大卫·g·西蒙斯](https://davidgs.com)上。