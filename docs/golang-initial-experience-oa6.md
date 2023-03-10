# GoLang -初步体验

> 原文：<https://dev.to/dbh/golang-initial-experience-oa6>

最近，我决定在一个新项目中试用 GoLang 来实现 CRUD API。最近，我在 Java、Groovy 和 Python 方面积累了很多经验。

我最近更多的体验是在 Java 或 Groovy 加上 Spring Boot 中进行的。我有点无聊了，为什么不去学点东西，找点乐子呢？

## 要求

以下是一些要求

*   设计和实现领域的数据模型
*   实现 CRUD API
*   Mongo 数据库后端
*   必须有 Swagger 定义，以记录 API 和促进生成各种语言的客户端。
*   在 Docker 容器中运行
*   可以部署到 Kubernetes

## 非功能需求

*   需要易于消费的 API，在其他语言。
*   需要能够快速移动(并可能打破东西)
*   必须有单元测试

## 美好的拥有

*   对内存消耗持保守态度。
    *   如果我想在内存受限的环境中运行，这一点很重要，比如具有 512 MB RAM 的 Raspberry Pi 2B。
*   需要有趣并且是一次学习经历

## 模块&库使用

| 功能 | 组件 |
| --- | --- |
| 数据库访问 | mongo-go-driver |
| 按指定路线发送 | 围棋 |
| REST API JSON 补丁 | json 补丁 |
| 单元测试 | 证明 |
| Swagger API 定义 | go-swagger |

### 好人

这种语言与 C 和 Java 惊人地相似。这两方面的背景使得学习足够熟练的围棋来完成一个入门项目变得非常容易。

我特别喜欢 GoLang 中代码的简单明了。

公平地说，我也喜欢高度固执己见的框架，尽可能少地使用锅炉板代码。Java 11+或 Groovy、Spring Boot、Spring Data、Project Lombok，或许还有 Spring Data REST 的组合也让我很开心。老实说，有时候 Spring Boot 数据休息的魔力有点太大了。

Go 的“defer”关键字可能是我最喜欢的功能之一。延迟一些动作直到函数退出，这是关闭资源和记录函数的退出动作的一种奇妙的浪费。

### 只是不同或尴尬

#### **错误处理，有点繁琐**

错误处理有点不同，来自 Java 背景。我发现在围棋中需要更加明确。

在 Java 中，一个方法可能抛出一个异常，捕获一个或多个异常，吞下它们(可能是一件坏事)，或者重新抛出供调用者处理，go 需要调用方法的模式，评估是否有错误。我们可以讨论这是好是坏。

我发现 Go 的错误检测和传递需要一点调整时间，有点乏味，但肯定是可行的。

//我经常在代码中看到这种模式

`obj1, err := doohickey.doSomething(someArg)
if err !=nil {
log.Println("doohickey.doSomething got error error: ", err)
return
}
obj2, err2 := widget.doSomethingElse(otherArg)
if err2 !=nil {
log.Println("Widget doSomethingElse returned error: ", err2)
return
}
//...`

#### **JSON 响应类型，到 Struct 映射**

关于 Go、JSON 和静态类型，我发现在 Go 中如何处理动态 JSON 并将其解析为结构有点令人困惑和尴尬。

这在 Groovy 和 Python 中要容易得多，因为它们完全可以将 JSON 动态转换成其他事物的映射。

在 Go 中，将 JSON 反序列化为一个结构，然后再将其序列化回来，这与其他语言没有本质上的区别。

### 在成熟期奋起直追

其实我并没有发现它有什么“不好”的地方。相反，我确实发现了正常情况下应该有的东西。由于 Go 是一种相对较新的语言，它在某些领域仍在追赶。

#### **围棋** **依赖和版本化模块库**

我曾经使用过依赖管理和构建工具，比如 Java 的 Gradle 和 Maven，我自然希望在 Go 中使用相同级别的依赖管理。

当我写这篇文章的时候， [Go Lang 1.13](https://golang.org/doc/go1.13) 发布了对 Google 模块代理的支持，它说:

> 从 go 1.13 开始，Go 命令默认使用 Google 运行的 Go 模块镜像和 Go 校验和数据库下载和验证模块。请参见[https://proxy.golang.org/privacy](https://proxy.golang.org/privacy)了解关于这些服务的隐私信息，并参见 [go 命令文档](https://golang.org/cmd/go/#hdr-Module_downloading_and_verification)了解配置细节，包括如何禁用这些服务器或使用不同的服务器。如果您依赖于非公共模块，请参见[文档来配置您的环境](https://golang.org/cmd/go/#hdr-Module_configuration_for_non_public_modules)。

## 总结思想

令人惊讶的是，学会足够好的 Go Lang 是非常容易的。我发现它令人愉快和有趣。增强 REST API、细化数据模型、生成 swagger doc 以及创建单元测试都是非常好的体验。

基于这次经历，我一定会学习更多的 GoLang。

## 进一步阅读

*   GoLang 1.11 模块[https://github . com/golang/go/wiki/modules](https://github.com/golang/go/wiki/Modules)
*   围棋中的错误处理，[https://github.com/golang/go/wiki/Modules](https://github.com/golang/go/wiki/Modules)