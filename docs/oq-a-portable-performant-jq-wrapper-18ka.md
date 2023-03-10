# OQ——一个可移植/高性能的 jq 包装器

> 原文：<https://dev.to/blacksmoke16/oq-a-portable-performant-jq-wrapper-18ka>

# oq

一个高性能、可移植的 jq 包装器，它有助于 JSON 之外的其他格式的消费和输出；使用 jq 过滤器转换数据。

## 背景

我已经使用 [jq](https://github.com/stedolan/jq) 将一个主 JSON 文档转换成依赖于合作伙伴的结构供他们使用了一段时间。然而，直到最近，所有的合作伙伴结构都在 JSON 中。由于`jq`本身不支持输出 XML，我开始四处查看是否有库允许使用`jq`过滤器来转换数据，但是除了 JSON 之外还输出 XML。我最终找到了一个名为 [yq](https://github.com/kislyuk/yq) 的 Python 库，看起来非常完美。

它支持输出到 XML 和 JSON，同时能够对两者使用相同的`jq`过滤器。在使用它一段时间后，我清楚地看到，虽然对于较小的文件来说速度很快，但对于我需要处理的一些较大的文档来说，它确实有些吃力。它是 Python 的事实也使事情变得复杂，因为需要安装 Python 来使用它，而不需要通过一些额外的过程来使它成为单一的二进制文件。因此，一个更高性能和更便携的选择的想法开始成形。

## 简介

使用相对较新的[水晶](https://crystal-lang.org/)语言；我创建了 [oq](https://github.com/blacksmoke16/oq) ，主要目标是可移植性、性能以及扩展`jq`支持的格式。

## 用法

如果序列化为 XML，除了根元素的名称之外，`oq`还有三个额外的参数，用于设置要使用的输入/输出格式。所有其他参数都传递给`jq`。

### 例子

#### 消耗 JSON 并输出 XML

```
echo '{"name": "Jim"}' | oq -o xml .
<?xml version="1.0" encoding="UTF-8"?>
<root>
  <name>Jim</name>
</root> 
```

Enter fullscreen mode Exit fullscreen mode

#### 消耗 JSON 并输出 YAML

```
echo '{"name": "Jim"}' | oq -o yaml .
--------
name: Jim 
```

Enter fullscreen mode Exit fullscreen mode

#### 从文件中消耗 YAML 并输出 XML

`data.yaml`

```
--------
name: Jim
numbers:
  - 1
  - 2
  - 3 
```

Enter fullscreen mode Exit fullscreen mode

```
oq -i yaml -o xml . data.yaml 
<?xml version="1.0" encoding="UTF-8"?>
<root>
  <name>Jim</name>
  <numbers>1</numbers>
  <numbers>2</numbers>
  <numbers>3</numbers>
</root> 
```

Enter fullscreen mode Exit fullscreen mode

#### 消费 JSON，转换它，输出 XML

`data.json`

```
{  "guests":  [  {  "name":  "Jim",  "age":  17,  "numbers":  [  1,  2,  3  ]  },  {  "name":  "Bob",  "age":  51,  "numbers":  [  4,  5,  6  ]  },  {  "name":  "Susan",  "age":  85,  "numbers":  [  7,  8,  9  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

`filter`

```
.guests  |  {  "person":  [  .[]  |  {  "age":  {  "@scale":  .scale,  "#text":  .age  },  "name":  .name,  "favorite_numbers":  {  "number":  .numbers  }  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

```
oq -o xml --xml-root people -f filter data.json
<?xml version="1.0" encoding="UTF-8"?>
<people>
  <person>
    <age scale="months">289</age>
    <name>Jim</name>
    <favorite_numbers>
      <number>1</number>
      <number>2</number>
      <number>3</number>
    </favorite_numbers>
  </person>
  <person>
    <age scale="years">51</age>
    <name>Bob</name>
    <favorite_numbers>
      <number>4</number>
      <number>5</number>
      <number>6</number>
    </favorite_numbers>
  </person>
  <person>
    <age scale="days">31025</age>
    <name>Susan</name>
    <favorite_numbers>
      <number>7</number>
      <number>8</number>
      <number>9</number>
    </favorite_numbers>
  </person>
</people> 
```

Enter fullscreen mode Exit fullscreen mode

处理 JSON 到 XML 代码转换的方法基于[这篇文章](https://www.xml.com/pub/a/2006/05/31/converting-between-xml-and-json.html)。

## 基准

我还为`jq`、`yq`和`oq`运行了一些[基准](https://github.com/stedolan/jq/wiki/X---Experimental-Benchmarks)，以展示它们在各种情况下的比较。

### 设置

OS: `#1 SMP Debian 4.9.168-1+deb9u3 (2019-06-16)`
CPU: `Intel i7-7700k`
内存:`32GB @ 3,000 MHz`
SSD: `Samsung 850 PRO - 512GB`

基准测试是通过`/usr/bin/time -v`命令完成的

#### 简单

首先，我使用了`data.json`文件来看看它们如何简单地解析文件并通过`.`过滤器输出它自己。

##### jq

```
jq . data.json | wc -l
    Command being timed: "jq . data.json"
    User time (seconds): 0.02
    System time (seconds): 0.01
    Percent of CPU this job got: 68%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.06
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 16236
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 3860
    Voluntary context switches: 224
    Involuntary context switches: 8
    Swaps: 0
    File system inputs: 0
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0
31 
```

Enter fullscreen mode Exit fullscreen mode

##### yq

```
yq . spec/assets/data1.json | wc -l
    Command being timed: "yq . data.json"
    User time (seconds): 0.08
    System time (seconds): 0.01
    Percent of CPU this job got: 77%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.11
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 16252
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 1
    Minor (reclaiming a frame) page faults: 7179
    Voluntary context switches: 189
    Involuntary context switches: 10
    Swaps: 0
    File system inputs: 1672
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0
31 
```

Enter fullscreen mode Exit fullscreen mode

##### oq

```
oq . data.json | wc -l
    Command being timed: "oq . data.json"
    User time (seconds): 0.02
    System time (seconds): 0.04
    Percent of CPU this job got: 74%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.10
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 16140
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 4499
    Voluntary context switches: 306
    Involuntary context switches: 13
    Swaps: 0
    File system inputs: 0
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0
31 
```

Enter fullscreen mode Exit fullscreen mode

对于第一个测试，这三个测试几乎是一样的，只有挂钟/内存使用的差异可以忽略不计。

#### Jeopardy.json (#2)

下一个基准使用在`jq`的基准 wiki 页面中检索到的`jeopardy.json` ~56mb 文件。

首先，一个简单的`length jeopardy.json`命令。

##### jq

```
jq length jeopardy.json 
216930
    Command being timed: "jq length jeopardy.json"
    User time (seconds): 0.64
    System time (seconds): 0.10
    Percent of CPU this job got: 97%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.76
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 230080
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 63213
    Voluntary context switches: 240
    Involuntary context switches: 13
    Swaps: 0
    File system inputs: 0
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0 
```

Enter fullscreen mode Exit fullscreen mode

##### yq

```
yq length jeopardy.json 
216930
    Command being timed: "yq length jeopardy.json"
    User time (seconds): 152.45
    System time (seconds): 1.27
    Percent of CPU this job got: 100%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 2:33.04
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 3853532
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 1117041
    Voluntary context switches: 13708
    Involuntary context switches: 3189
    Swaps: 0
    File system inputs: 0
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0 
```

Enter fullscreen mode Exit fullscreen mode

##### oq

```
oq length jeopardy.json 
216930
    Command being timed: "oq length jeopardy.json"
    User time (seconds): 0.67
    System time (seconds): 0.17
    Percent of CPU this job got: 105%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.80
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 230224
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 63839
    Voluntary context switches: 13832
    Involuntary context switches: 12
    Swaps: 0
    File system inputs: 0
    File system outputs: 0
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0 
```

Enter fullscreen mode Exit fullscreen mode

大文件对`yq`来说不是好兆头，它比`oq`或`jq`多花了大约 190 倍的时间，同时还多使用了将近 17 倍的内存。

#### YAML = > XML

我做的最后一个基准测试是给`yq`和`oq`一个大的 yaml 文件(~57mb)，然后让他们把它转换成 XML。既然`jq`不能消耗`YAML`，我就把它排除了。

使用的文件:`invItems.yaml`从 [EVE Online SDE 导出](https://cdn1.eveonline.com/data/sde/tranquility/sde-20190625-TRANQUILITY.zip)。

示例输入:

```
-   flagID: 0
    itemID: 0
    locationID: 0
    ownerID: 0
    quantity: -1
    typeID: 0
-   flagID: 0
    itemID: 1
    locationID: 0
    ownerID: 0
    quantity: -1
    typeID: 0
  ... 
```

Enter fullscreen mode Exit fullscreen mode

##### yq

对于 yq，我必须给它一个过滤器和一些额外的参数，让它正确输出

```
yq -s -x --xml-root items --xml-dtd '{"item": .[] | .}' invItems.yaml > invItems.yq.xml
    Command being timed: "yq -s -x --xml-root items --xml-dtd {"item": .[] | .} invItems.yaml"
    User time (seconds): 309.21
    System time (seconds): 2.76
    Percent of CPU this job got: 100%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 5:11.90
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 7817608
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 2262904
    Voluntary context switches: 32918
    Involuntary context switches: 2504
    Swaps: 0
    File system inputs: 0
    File system outputs: 195072
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0 
```

Enter fullscreen mode Exit fullscreen mode

示例输出

```
<?xml version="1.0" encoding="utf-8"?>
<items>
  <item>
    <flagID>0</flagID>
    <itemID>0</itemID>
    <locationID>0</locationID>
    <ownerID>0</ownerID>
    <quantity>-1</quantity>
    <typeID>0</typeID>
  </item>
  <item>
    <flagID>0</flagID>
    <itemID>1</itemID>
    <locationID>0</locationID>
    <ownerID>0</ownerID>
    <quantity>-1</quantity>
    <typeID>0</typeID>
  </item>
  ...
</items> 
```

Enter fullscreen mode Exit fullscreen mode

##### oq

```
oq -i yaml -o xml --xml-root items . invItems.yaml > invItems.oq.xml
    Command being timed: "oq -i yaml -o xml --xml-root items . invItems.yaml"
    User time (seconds): 20.08
    System time (seconds): 0.48
    Percent of CPU this job got: 107%
    Elapsed (wall clock) time (h:mm:ss or m:ss): 0:19.13
    Average shared text size (kbytes): 0
    Average unshared data size (kbytes): 0
    Average stack size (kbytes): 0
    Average total size (kbytes): 0
    Maximum resident set size (kbytes): 1332328
    Average resident set size (kbytes): 0
    Major (requiring I/O) page faults: 0
    Minor (reclaiming a frame) page faults: 522235
    Voluntary context switches: 30478
    Involuntary context switches: 974
    Swaps: 0
    File system inputs: 0
    File system outputs: 195072
    Socket messages sent: 0
    Socket messages received: 0
    Signals delivered: 0
    Page size (bytes): 4096
    Exit status: 0 
```

Enter fullscreen mode Exit fullscreen mode

示例输出

```
<?xml version="1.0" encoding="UTF-8"?>
<items>
  <item>
    <flagID>0</flagID>
    <itemID>0</itemID>
    <locationID>0</locationID>
    <ownerID>0</ownerID>
    <quantity>-1</quantity>
    <typeID>0</typeID>
  </item>
  <item>
    <flagID>0</flagID>
    <itemID>1</itemID>
    <locationID>0</locationID>
    <ownerID>0</ownerID>
    <quantity>-1</quantity>
    <typeID>0</typeID>
  </item>
  ...
</items> 
```

Enter fullscreen mode Exit fullscreen mode

与`jeopary.json`基准测试类似，`yq`只是在处理较大的输入时遇到了困难，这个测试用例比`oq`多花了大约 16 倍的时间，使用了几乎 6 倍的内存。

## 道路走向 1.0.0

因为这个项目仍然处于开发的早期，所以在命名为`1.0.0`之前，我把我想做的事情的路线图放在了一起:

*   支持 XML 输入格式
*   解决出现的错误/问题
*   小功能请求
*   可能的附加格式

请随意提交问题/PRs。