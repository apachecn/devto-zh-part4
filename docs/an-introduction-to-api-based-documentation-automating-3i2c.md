# 基于 API 的文档自动化介绍

> 原文：<https://dev.to/eviltester/an-introduction-to-api-based-documentation-automating-3i2c>

*TLDR；正式的 api 文档规范生态系统具有工具，可以帮助更轻松地创建文档，并使用文档自动验证，例如 Dredd、Swagger 和 StopLight*

我正在为碎浆机开发一个 API，我想以正式的规范编写文档，并使用工具来验证它。听起来很容易。

Pulper 是一个简单的 CRUD 应用程序，它是我的 TestingApp 的一部分，testing app 是一个应用程序集合，用作示例应用程序来练习测试:

*   [https://github.com/eviltester/TestingApp](https://github.com/eviltester/TestingApp)

如果你想玩的话，heroku 上有一个没有 API 的版本。

*   [https://thepulper.herokuapp.com](https://thepulper.herokuapp.com)

我正在添加一个 API，以便它可以支持 API 探索，这样我就可以用 JavaScript 将 GUI 与 API 后端挂钩，并允许出现一类全新的紧急错误。

使 Pulper 稍微不同的一点是，在管理下拉菜单中，你会发现有多个版本的应用程序，你可以在它们之间切换。如果你是自动化的，这些提供了稍微不同的功能，潜在的不同的错误，和不同的 GUI 体验。

一旦我添加了一个 API，我就可以有不同的版本，有不同的错误等等。

## 记录和测试

API 的问题是，我想做得比我的 REST Listicator 测试应用程序好一点，你也可以在线玩，或者作为测试应用程序的一部分下载。

*   [https://rest-list-system.herokuapp.com/](https://rest-list-system.herokuapp.com/)

这方面的文档是手工制作的——这很好，因为它允许错误悄悄进入，这必须进行测试，但要理解 API 并不容易。

因为这个原因，我怀疑碎浆机 API 的版本 1 可能有手写文档。

## 标准文档格式

API 有标准的文档格式。最受欢迎的两个似乎是:

*   Swagger's OpenAPI
*   API 蓝图

您可以在以下网址找到关于 OpenAPI 的信息

*   [https://github . com/OAI/open API-Specification/blob/master/versions/2.0 . MD](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
*   [https://swagger.io/docs/specification/about/](https://swagger.io/docs/specification/about/)

和 https://apiblueprint.org/的 API 蓝图

各种格式之间的转换工具似乎已经存在，所以我认为从哪一种开始并不重要。

## 测试文档

文档是我们测试模型的输入之一。

*   文档中的东西存在吗？
*   我们能按照文档上说的去做吗？
*   系统看起来和操作起来像文档吗？
*   等等。

正式的文档格式提供了工具帮助的可能性。

围绕 API 格式的工具生态系统提供了诱人的前景，能够从正式规范中自动测试 API。

## 测试解释文档

工具会有所帮助，但主要是帮助“验证”请求和对规范的响应，而不是测试它。

我还没有充分探索工具空间，看看它们能走多远。

我看到的第一个工具是 Dredd

## [千代子](#dredd)

[https://dredd.org/en/latest/](https://dredd.org/en/latest/)

开箱即用，Dredd 可以采用 API 蓝图规范或 Swagger 规范:

*   lint 它来检查规范是否是有效的格式
*   发出所有 2xx 状态代码相关的请求

发出所有 2xx 状态代码请求并不像看起来那么有用，因为它试图发出 POST 请求来接收 201，但是这样做时没有数据，所以您会得到一个失败的测试。如果你把模式文件写得很好，那么 Dredd 可能会在规范中找到一些例子，但是我还没有试验过。

但是我发现它非常有用，开箱后没有配置:

*   发出的请求列表
*   实际上看到一些经过
*   看到一些 API 与规范不匹配的有效错误

我认为它增加了开箱即用的价值。

## [钻钩](#dredd-hooks)

Dredd 有钩子允许编写脚本，我试验过将有效负载体添加到请求中，并跳过任何我不想看到失败的响应代码。效果很好。

使用`--names`命令行参数来找出钩子事务名称

~ ~ ~ ~ ~~dredd swagger . JSON[http://localhost:4567](http://localhost:4567)-人名
~ ~ ~~~

我为使用 Dredd 添加了一个简单的`hooks.js`。这个:

*   为 my POST books 添加一个有效负载，以创建一个项目并触发 201 状态。
*   跳过一个我还没有编码的事务

~ ~ ~ ~ ~~var hooks = require(' hooks ')；~~

hooks . before('/apps/pulp/API/books >创建或修改单个或多个图书> 201 > application/json '，(transaction)= > {
transaction . request . body = JSON . stringify({
" books ":[
{
" title ":"小人国恐怖之地"，
"出版年份":1980 年，
"seriesId ":"第 1 期"，
"作者":[
{
"作者
})；
hooks . before('/apps/pulp/API/books>创建或修改单个或多个 books>200>application/JSON '，(transaction)=>{
transaction . skip = true；
})；
~ ~ ~

Dredd 看起来有一套很好的轻量级增强方法来添加额外的信息，允许未接触的文档帮助驱动一些自动化执行。

## 工装

我发现用在线 swagger 编辑器编写 swagger 规范非常耗时

但是使用 stoplight.io 要快得多

[https://stoplight.io/](https://stoplight.io/)

我目前正在进行的 api 文档工作就在这里，但是这可能会有很大的变化。

[https://next.stoplight.io/eviltester-1/thepulper](https://next.stoplight.io/eviltester-1/thepulper)

我将对正式的 api 文档规范和相关工具进行更多的试验，看看是否有任何更有用的工具和方法可以添加到我的 API 测试过程中。

* * *

如果您对测试和自动化 API 感兴趣，那么您可能会发现我的书“[自动化和测试 REST API](https://www.eviltester.com/page/books/automating-testing-api-casestudy/) ”很有用。它包括从头开始测试和自动化 API，并使用 cURL、proxy、Postman、RestAssured 等工具，讨论自动化的抽象层。