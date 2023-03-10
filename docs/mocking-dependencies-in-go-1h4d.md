# 模仿 Go 中的依赖关系

> 原文：<https://dev.to/jonfriesen/mocking-dependencies-in-go-1h4d>

Go 使得显而易见的非魔法依赖注入变得简单。当引入非简单依赖时，如数据库和第三方服务(如 API 客户端)，许多新开发人员或来自其他语言的有经验人员经常会遇到麻烦。这就是我和许多其他人陷入困境的地方，需要找到一种测试代码而不触及依赖代码的方法。我打算展示一个迄今为止对我很有效的策略。

让我们从一千英尺的角度来看我们将要解决的问题。我们的软件使用第三方服务，通过 restful HTTP API 访问。我们对这个 API 的每次调用都要花钱，这使得用我们的单元测试调用这个 API 变得不可行。我们的目标是能够在我们的测试周期中用一个我们控制的 API 客户端来切换这个 API 客户端。

假设我们有一个类似于
的`main.go`

```
// main.go
package main 

import thirdPartySvc "url.com/thirdsPartySvcClient"
import "myservice"

// returns a new third party service client
client, err := thirdPartySvc.New("OurAPIKey")
handleError(err)

// we create a new instance of our service
svc := myservice.New(client)
// start the service
svc.Start() 
```

Enter fullscreen mode Exit fullscreen mode

我们的`myservice/svc.go`文件看起来像:

```
// myservice/svc.go
package myservice

import thirdPartySvc "url.com/thirdsPartySvcClient"

type MyService struct {
    client thirdPartySvc.Client
}

func New(client thirdPartySvc.Client) MyService {
    return MyService{client}
}

func (s *MyService) Start() {
    // start the service
    data := s.client.Get()
    // do something with `data`
    data = data + "-validated"
    return data
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很不错吧？如果你已经沉迷于即将暴露的问题，你可能已经畏缩了。这个设置的问题是`myservice`包依赖于`thirdPartySvc`库。当我们测试它时，我们必须传入一个从该库创建的结构，它将调用真实世界的服务。

我们使其可测试的第一步是移除`thirdPartySvc`依赖性。这可以通过接口来实现。

```
// myservice/svc.go
package myservice

type ThirdPartyInterface interface {
    // functions that we use from the third party service client
    func Get() string
}

type MyService struct {
    client ThirdPartyInterface
}

func New(client ThirdPartyInterface) MyService {
    return MyService{client}
}

func (s *MyService) Start() string {
    // start the service
    data := s.client.Get()
    // do something with `data`
    data = data + "-validated"
    return data
} 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个接口，它只定义了我们在`thirdPartySvc`结构中使用的**和**函数。这很酷，有几个原因，没有对外部库的硬依赖，我们可以传入任何匹配接口签名的结构，我们确切地知道哪些函数正在被使用，以及它们是如何被使用的。

现在我们可以在不调用外部服务的情况下测试我们的`MyService.Start()`函数。

```
// svc_test.go
package myservice

type MockThirdPartySvc struct {}

func (m *MockThirdPartySvc) Get() string {
    return "test-string"
}

func TestMyService_Start(t *testing.T) {
    expected := "test-string-validated"
    startString := New(MockThirdPartySvc{})

    if startString != expected {
        t.Errorf("Expected %s but got %s", expected, startString)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个测试有效，因为我们的`MockThirdPartySvc`结构函数匹配那些在`svc.go`中定义的`ThirdPartyInterface`。请注意，虽然`thirdPartySvc` struct 可能有比我们使用的更多的函数，但我们只实现我们需要的函数，这使得测试更容易，并理解我们如何使用该库的范围。

在这一点上，你可能会说，这太棒了，但当我有许多使用库提供的函数的不同组合的包时，就开始崩溃了。在每个包中创建复杂的模拟是一项繁重的工作，维护起来也很痛苦。你完全正确。

我使用两部分的方法创建一个`mocks`包，它可以在所有的测试中访问，并且包含项目使用的最完整的 API 接口。再深入一点，其他测试文件无法访问`*_test.go`文件中定义的公共结构。创建一个模拟包允许这种可访问性。通过在模拟包中定义一个接口，该接口包含代码库为该库使用的所有函数，即使在非测试代码中定义了子集接口，也可以保证它能够工作。

`mocks`包中接口的实现可以用各种聪明的方法创建，一种方法是手工创建简单的实现，调用方可以传入函数。

```
package mocks

type ThirdPartyInterface interface {
    // all of the functions we use from the third party client
    func Get() string
    func Post(string) error
}

type ThirdPartyMock struct {
    GetFunc: func() string
    PostFunc: func(string) error
}

func (m *ThirdPartyMock) Get() string {
    return m.GetFunc()
}

func (m *ThirdPartyMock) Post(in string) error {
    return m.PostFunc(string)
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法让测试定义特定测试用例所需的内部函数，因此相同的模拟可以用于一组具有定制结果的测试。

#### 嘲弄一代

这种模拟创建的方法很繁琐，可以通过工具和代码生成来简化。一些工具如 [golang/mock](https://github.com/golang/mock) 或者我喜欢的库 [matryer/moq](https://github.com/matryer/moq) 。

生成可以通过几种方式完成，包括一次性生成、内联生成或作为构建命令的一部分。一次性生成，仅仅是一个正在运行的命令(例如`moq -out ThirdParty_test.go -pkg mocks myservice ThirdPartyInterface`)。

还有内联生成，您可以在其中标注所需的模拟(例如`//go:generate moq -out ThirdParty_test.go -pkg mocks myservice ThirdPartyInterface`)，这将在`go generate`运行时生成模拟。

最后一种也是我最喜欢的方法是识别出想要的模拟，并在外部生成它们。一个这样的例子可以在 [digitalocean/doctl](https://github.com/digitalocean/doctl) 项目中找到，一个 [regenmocks.sh 脚本](https://github.com/digitalocean/doctl/blob/master/scripts/regenmocks.sh)连接到他们的 [Makefile](https://github.com/digitalocean/doctl/blob/master/Makefile) ，具体来说:

```
# executed with make mocks .PHONY: mocks
mocks:
    @echo "==> update mocks"
    @echo ""
    @scripts/regenmocks.sh 
```

Enter fullscreen mode Exit fullscreen mode

#### 给库作者的一个提示

我经常发现包含一个完整的库公共接口对使用你的库的开发者来说是一种礼貌。我相信[接口应该在它们被使用的地方被创建](https://github.com/golang/go/wiki/CodeReviewComments#interfaces)并且库返回结构(例如创建一个客户端)。也就是说，对于消费者来说，在库中拥有一个完整的接口是轻松生成模拟的好方法。如果您的库经常被模拟出来进行测试，您甚至可以考虑添加一个包含生成的模拟的模拟目录，您可以使用这些模拟在内部测试库，并且您的用户也可以使用它们作为一个不错的奖励。

#### 最终

我希望这描绘了一幅提高测试质量的图画，并且在测试具有难以测试的依赖关系的代码时，让您的生活变得轻松一些。如果您有任何反馈或问题，请随时通过 twitter 或电子邮件与我联系。

> 这篇文章最初发布在我的网站:[https://jonfriesen.ca/blog/mocking-dependencies-in-go/](https://jonfriesen.ca/blog/mocking-dependencies-in-go/)