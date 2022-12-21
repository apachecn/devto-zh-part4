# 开放源代码和无服务器的多语言

> 原文：<https://dev.to/upkarlidder/polyglot-with-open-source-and-serverless-8fp>

作为 IBM 的开发人员倡导者，我经常指导黑客马拉松，团队面临的一个常见问题是为他们的解决方案选择哪种语言。一般来说，最初想出这个主意的人会决定使用哪种语言。那比分析瘫痪要好，然而还有更好的方法！如果您可以将应用程序划分为多个服务和/或功能，那么您可以使用一个多语言框架，比如 openwhisk。

> OpenWhisk 平台支持一种编程模型，其中开发人员以任何支持的编程语言编写功能逻辑(称为 [**【动作】**](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions.md#openwhisk-actions) ，可以动态调度和运行，以响应来自外部源([**【Feeds】**](https://github.com/apache/incubator-openwhisk/blob/master/docs/feeds.md#implementing-feeds))或来自 HTTP 请求的关联事件(通过 [**触发器**](https://github.com/apache/incubator-openwhisk/blob/master/docs/triggers_rules.md#creating-triggers-and-rules) )。

使用这样的框架，您可以

1.  并行编写多个函数，提高团队的效率。
2.  使用多种语言，确保每个人都能尽最大努力参与。

如果你是 OpenWhisk 架构的新手，让我们来做一个快速速成课程💥！

*   动作→在无服务器平台上运行的代码
*   触发器→外部事件发生时触发
*   规则→将触发器连接到操作
*   打包→捆绑或模块化您的操作、触发器和规则，并与其他代码共享

让我们在 OpenWhisk 上重现一下[詹姆斯·托马斯](https://medium.com/u/604ac4ebfc5f)在他精彩的[教程/训练营中提供的这个简单例子。](https://github.com/IBM-Cloud/openwhisk-workshops/tree/master/bootcamp?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-OInfluencer-Medium-USL-openwhisk-workshop-sequence&cm_mmca1=000037FD&cm_mmca2=10010797)[练习 1.2](https://github.com/IBM-Cloud/openwhisk-workshops/tree/master/bootcamp?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-OInfluencer-Medium-USL-openwhisk-workshop-sequence&cm_mmca1=000037FD&cm_mmca2=10010797) 提供了一个动作序列的例子，简单来说就是一个动作调用另一个动作，后者又调用第三个动作。一个动作的结果会传递给下一个动作。如果有一个错误，整个序列出错。我们将使用 Javascript、Python 和 Swift，而不是用单一语言实现整个序列！多酷啊！以下是一些要求

1.  Split:应该接收一个字符串并返回一个由空格分隔的单词数组。我们将在 **Javascript** 中编写这个函数。
2.  Reverse:应该接受一个数组并返回一个与输入数组相反的新数组。我们将在 **Swift** 中编写这个函数。
3.  Join:接受一个数组并返回一个新数组，该数组是元素的空格分隔的连接。我们将用 **Python** 编写这个函数。

例如，如果我将“hello world”传递给这个序列，我将返回“world，hello”。这些功能看起来像什么？

#### 创建动作

**splitjs Javascript 动作**

```
function main(params) {
    var text = params.text || "";
    console.log(`incoming text: ${text}`)
    var words = text.split(' ');
    return {words: words}
} 
```

Enter fullscreen mode Exit fullscreen mode

**reverse Swift Swift Swift action**

```
struct Input: Codable {
    var words: [String]?
}
struct Output: Codable {
    let message: [String]
}
func main(param: Input, completion: (Output?, Error?) -> Void) -> Void {

    var result = Output(message: [] )

    if let words = param.words{
        result = Output(message: words.reversed() )
    }
    completion(result, nil)
} 
```

Enter fullscreen mode Exit fullscreen mode

**joinpy Python 动作**

```
import sys

import json

def main (dict):

  print ('dict ' + json.dumps(dict))

  if "message" in dict:

     return {'message': ', '.join(dict['message'])}

  else:

     return { 'message': [] } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！下面三行代码 ***将*** 这些代码片段部署为 *openwhisk 上的动作。*

```
wsk action create splitjs splitjs.js
wsk action create reverseswift reverseswift.swift
wsk action create joinpy joinpy.py 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来测试一下。

#### 分别测试我们的行动

**测试拆分 javascript 动作**

```
$ wsk action invoke splitjs -r -p text "hello world"

{  
 "words": [  
 "hello",  
 "world"  
 ]  
} 
```

Enter fullscreen mode Exit fullscreen mode

**测试反向迅捷动作**

```
$ wsk action invoke reverseswift -r -p words '["hello","world"]'

{  
 "message": [  
 "world",  
 "hello"  
 ]  
} 
```

Enter fullscreen mode Exit fullscreen mode

**测试加入 python 动作**

```
$ wsk action invoke joinpy -r -p message '["world", "hello"]'

{  
 "message": "world, hello"  
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个序列

我们可以创造一个序列

split js→reversift→join py

```
wsk action create split-reverse-join --sequence splitjs,reverseswift,joinpy 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来测试一下！

```
$ wsk action invoke split-reverse-join -r -p text "Hello World"

{  
 "message": "World, Hello"  
} 
```

Enter fullscreen mode Exit fullscreen mode

多酷啊！让我们用钱德勒舞来庆祝吧！

[![](img/8aff9cd316ad369e45543f78ad2e660e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4M57HT44--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AQCks4cIzllXVqUltOeecpw.gif)

如果你在旧金山湾区，请在 08/08/19 和我一起参加一个关于 OpenWhisk 的[实践研讨会。你](https://www.meetup.com/IBM-Developer-SF-Bay-Area-Meetup/events/263172151/)[可以在这里](https://www.meetup.com/IBM-Developer-SF-Bay-Area-Meetup/events/263172151/)注册。 [IBM 开发人员](https://medium.com/u/262975298e3a)和 API 向导 [Max Katz](https://medium.com/u/bf01a11701fe) 将会出现。