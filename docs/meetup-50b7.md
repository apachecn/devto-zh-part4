# Meetup

> 原文：<https://dev.to/gsarwate/meetup-50b7>

## 简介

这是一个针对 exercism.io 问题‘Meetup’的分步解决方案，可以在这里看到[(需要登录)。](https://exercism.io/my/solutions/ac5366bdb77c44c195eddcea4d92c60a)

## 问题

计算聚会的日期。

通常聚会在一周的同一天举行。在本练习中，您将获取 meetup 日期的描述，并返回实际的 meetup 日期。

一般描述的例子有:

*   2017 年 1 月的第一个星期一
*   2017 年 1 月的第三个星期二
*   2017 年 1 月的婚礼
*   2017 年 1 月的最后一个星期四

您需要解析的描述符是:第一、第二、第三、第四、第五、最后、monteenth、tuesteenth、wednesteenth、thursteenth、friteenth、saturteenth、sunteenth

注意“monteenth”、“tuesteenth”等都是造词。有一次聚会，其成员意识到一个月中正好有 7 个数字日以“-teenth”结尾。因此，可以保证一周中的每一天(星期一，星期二，...)在每个月都会有一个以“-teenth”命名的日期。

给定 meetup 日期的例子，每个包含月、日、年和描述符，计算实际 meetup 的日期。例如，如果给定“2017 年 1 月的第一个星期一”，则正确的 meetup 日期是 2017/1/2。

## 解

由于冗长的描述和大量的测试案例，这个问题可能看起来很复杂，但是通过一步一步地将我们得到的信息分解成更小的部分，就可以解决这个问题。作为一种函数式声明性语言，Elixir 非常适合这种方法。

让我们看一下测试，以获得关于给定输入和预期输出的更多细节。

给定输入:

1.  年份(4 位数)
2.  月份(1 位数或 2 位数)
3.  星期几(用于工作日的原子，例如:星期一、星期二等。)
4.  时间表(第一周)，第二周(第二周)...teenth(以 teenth 结尾的七天，例如 13 号))

预期产出:

带有{yyyy，mm，dd}的元组

### 步骤

如果我们仔细研究这个问题，我们会发现我们需要根据一周中的某一天和一个时间表来确定聚会的日期。该计划基于七天的时间范围。因此，首先我们需要找出七天的范围和该范围内的日期。一旦我们找到了日期，我们就可以找到这些日期在一周中的哪几天。最后，我们可以选择与请求的星期几相匹配的日期来找到答案。

包含步骤的高级代码看起来像这样

```
find_day_range(year, month, schedule)
|> find_date(year, month, weekday) 
```

#### 第一步-找到七天的范围

七天范围基于计划、年份和月份。该范围内的日期可以是固定的，也可以是变化的。

##### 固定天数范围

第一、第二、第三和第四周内的日期是固定的，例如，第一周是从每月的 1 号到 7 号，第二周是从每月的 8 号到 14 号，依此类推。同样,“十月”计划也有固定的日期——从每月 13 号到 19 号(也就是 7 天或一周)。所以我们可以定义固定范围如下:

```
@day_range_map %{
  first: 1..7,
  second: 8..14,
  third: 15..21,
  fourth: 22..28,
  teenth: 13..19
}

name: day_range_map
type: map
key: schedule
value: day range 
```

##### 可变天数范围

上周日程表中的日期会有所不同，取决于月份和年份。一月、三月、五月、七月、八月、十月和十二月有 31 天，除了二月，其他月份有 30 天。二月闰年有 29 天，非闰年有 28 天。因此，我们在计算上周的二月份时，也需要考虑年份。在上述所有条件下，我们上周包含以下日期范围。

```
22 23 24 25 26 27 28            # For month with 28 days
   23 24 25 26 27 28 29         # For month with 29 days
      24 25 26 27 28 29 30      # For month with 30 days
         25 26 27 28 29 30 31   # For month with 31 days 
```

因此，根据月份和年份的不同，上周的日期在 22 和 31 之间。我们可以用给定的月份和年份找到天数。天数与该月的最后一天相同。我们将使用 Erlang 函数:calendar . last _ day _ of _ the _ month/2 来找出一个月的最后一天。范围将在该月的最后一天到该日之前的六天之间。

##### 查找量程的功能

在这个函数中，我们比较时间表。如果时间表不是':last '，我们使用 day_range_map 查找范围。“For”:最后一个计划我们按照上面“可变天数范围”中的说明计算范围。

```
Function name: find_day_range

Function input: year, month, schedule

Function body:

case schedule do
  :last ->
    last_day_of_the_month = :calendar.last_day_of_the_month(year, month)
    (last_day_of_the_month - 6)..last_day_of_the_month

  _ ->
    @day_range_map[schedule]
end

Function output: range 
```

或者，我们可以使用 Elixir 函数 Date.days_in_month/1 来计算一个月中的天数。函数需要日期作为参数，我们可以用 Date.from_erl！/1 函数获取返回日期符号的日期，例如~D[2019-01-13]。Date.from_erl！/1 函数需要{年、月、日}元组作为参数。我们已经有年和月了。我们将 1(每月的第一天)作为一天。

```
date = Date.from_erl!({year, month, 1})
Date.days_in_month(date) 
```

#### 第二步-查找日期

##### 查找步骤 1 中范围内所有日期的星期几

现在给定一个范围、年和月，我们想找出该范围内每天的工作日。范围有连续的七天，因此我们将为范围中的每一天指定唯一的工作日。

我们需要使用映射%{星期几= > {年，月，日}}来创建字典

如果我们知道日期，让我们找出如何得到星期几。在《长生不老药》中，一周中的第一天是从 1 到 7，从周一到周日。因此，星期一是 1，星期日是 7。例如，如果年份是 2019 年，月份是 1，则 2019 年 1 月 13 日是 7(星期日)，2019 年 1 月 14 日是 1(星期一)，依此类推。

让我们看看如何找出 2019 年 1 月 13 日是星期几。我们将使用 IEx 开始。

使用 Erlang 函数:calendar.day_of_the_week/3

```
iex(1)> :calendar.day_of_the_week(2019, 1, 13)
7 
```

现在，我们可以为范围中的每个元素找到星期几。我们可以使用 Enum.reduce/3 函数将范围缩小到%{星期几= > {年、月、日}}的映射。

```
iex(1)> year = 2019
2019
iex(2)> month = 1
1
iex(3)> range = 13..19
13..19
iex(4)> range |>
...(4)> Enum.reduce(%{}, fn day, acc ->
...(4)> Map.put(acc, :calendar.day_of_the_week(year, month, day), {year, month, day})
...(4)> end)
%{
  1 => {2019, 1, 14},
  2 => {2019, 1, 15},
  3 => {2019, 1, 16},
  4 => {2019, 1, 17},
  5 => {2019, 1, 18},
  6 => {2019, 1, 19},
  7 => {2019, 1, 13}
}

Note: the map is ordered by day of week. 
```

或者，我们可以使用 Elixir 函数 Date.day_of_week/1 来查找星期几。此函数需要日期符号作为参数，例如~D[2019-01-13]。我们有三条信息——年、月和日。因此，我们将首先创建印记。酏剂 Date.new/3 将创建基于年，月，日的印记。

```
iex(1)> Date.from_erl!({2019, 1, 13})
~D[2019-01-13] 
```

现在我们可以找到星期几了

```
iex(2)> Date.from_erl!({2019, 1, 13}) |> Date.day_of_week()
7 
```

2019 年 1 月 13 日是周日。所以预期答案是 7。

##### 将工作日参数与星期地图匹配

因为我们知道 weekday，所以我们将在上面的范围中找出匹配的 weekday

匹配工作日的日期就是答案。

现在，我们需要根据输入中请求的工作日找出地图条目。如果我们要查找 Sunday，那么我们需要找到 key 7 的值。我们得到的是工作日的原子，例如:星期天。我们需要把它映射到数字 7。为了实现这一点，我们可以创建以下地图:

```
@weekday_map %{
  monday: 1,
  tuesday: 2,
  wednesday: 3,
  thursday: 4,
  friday: 5,
  saturday: 6,
  sunday: 7
}

name: weekday_map
type: map
key: weekday
value: weekday in number 
```

我们可以使用上面的映射从上面创建的映射中获取值。

```
Map.fetch!(@weekday_map[weekday]) 
```

所以完成的函数看起来如下:

```
Function input: range, year, month, weekday

Function body:

range
|> Enum.reduce(%{}, fn day, acc ->
  Map.put(acc, :calendar.day_of_the_week(year, month, day), {year, month, day})
end)
|> Map.fetch!(@weekday_map[weekday])

Function output: {year, month, day} 
```

使用 Date.day_of_week/1:
获得星期几的替代药剂代码

```
Map.put(acc, Date.day_of_week(Date.from_erl!({year, month, day})), {year, month, day}) 
```

我们也可以用 Enum.find/2 代替地图。fetch！如果我们决定这样做，我们可以创建元组列表，并找到工作日的匹配日期。

```
Function input: range, year, month, weekday

Function body:

range
|> Enum.map(fn day ->
  {:calendar.day_of_the_week(year, month, day), {year, month, day}}
end)
|> Enum.find(fn {x, dt} -> x == @weekday_map[weekday] end)

Function output: {year, month, day} 
```

使用 Date.day_of_week/1:
获得星期几的替代药剂代码

```
{Date.day_of_week(Date.from_erl!({year, month, day})), {year, month, day}} 
```

### 最终代码

```
@weekday_map %{
  monday: 1,
  tuesday: 2,
  wednesday: 3,
  thursday: 4,
  friday: 5,
  saturday: 6,
  sunday: 7
}

@day_range_map %{
  first: 1..7,
  second: 8..14,
  third: 15..21,
  fourth: 22..28,
  teenth: 13..19
}

def meetup(year, month, weekday, schedule) do
  find_day_range(year, month, schedule)
  |> find_date(year, month, weekday)
end

defp find_day_range(year, month, schedule) do
  case schedule do
    :last ->
      last_day_of_the_month = :calendar.last_day_of_the_month(year, month)
      (last_day_of_the_month - 6)..last_day_of_the_month

    _ ->
      @day_range_map[schedule]
  end
end

defp find_date(range, year, month, weekday) do
  range
  |> Enum.reduce(%{}, fn day, acc ->
    Map.put(acc, :calendar.day_of_the_week(year, month, day), {year, month, day})
  end)
  |> Map.fetch!(@weekday_map[weekday])
end 
```