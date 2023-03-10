# 用于缩放和测试 JavaScript 的接口

> 原文：<https://dev.to/yawaramin/interfaces-for-scaling-and-testing-javascript-1daj>

我最近一直在使用 JavaScript 类型检查器 Flow，这证实了我的怀疑，即*接口*是 Flow 和 TypeScript 的“杀手级应用”。它们在两种类型检查器中的功能风格非常相似——你几乎会认为它们的设计者合作开发了它们的功能。最重要的是，由于这一功能，它们保留了许多 JavasScript 的“动态”外观，同时用可靠的类型安全对其进行备份。

下面是一个示例接口(在 Flow 中，但正如我所说的，TypeScript 非常相似):

```
export interface WeatherService {
  getForecast(
    city: string,
    numDays: number,
    tempUnit?: TempUnit,
  ): Promise<Forecast[]>,
}

/** Celsius or Fahrenheit */
export type TempUnit = 'C' | 'F'

export interface Forecast {
  city: string,
  day: {year: number, month: number, day: number},
  tempUnit: TempUnit,
  highTemp: number,
  lowTemp: number,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 快速回顾

Flow 和 TypeScript 中的接口描述了 JavaScript 对象的*形状*。记住，在 JavaScript 中，很多不同的东西都是对象——从像`{a: 1, b: true}`这样的记录样式的对象文字开始，到像`new Date()`这样的类实例，到像`[1, 2]`这样的数组，甚至像`x => x + 1`这样的函数。接口可以描述它们，从标准属性如`Forecast#city`到方法如`WeatherService#getForecast`，等等。

更重要的是，接口可以在事实之后描述对象*，*也就是在对象/类/等等之后。已经被定义了。这使得它们比你可能已经习惯的面向对象语言的传统接口不那么笨重，在面向对象语言中，类需要显式地*实现*接口(在 TS/Flow 中也可以，但不需要)。

## 接口一致性

更好的是:接口只要求一致性对象至少包含*列出的*成员。也就是说，他们不在乎是否包含更多的成员。例如，以下类别的对象将符合`WeatherService` :

```
/** `baseUri` must not end with a slash */
export default function liveWeatherService(
  baseUri: string,
): WeatherService {
  return new LiveWeatherService(baseUri)
}

class LiveWeatherService {
  baseUri: string

  constructor(baseUri: string) {
    this.baseUri = baseUri
  }

  async getForecast(
    city: string,
    numDays: number,
    tempUnit?: TempUnit = 'C',
  ): Promise<Forecast[]> {
    const response = await fetch(
      `${this.baseUri}/forecast?city=${city}&numDays=${numDays}&tempUnit=${tempUnit}`,
    )
    const json = await response.json()

    // Assume a function to decode raw JSON to `Forecast` type
    return decodeForecasts(json)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

导出的函数实现了一个巧妙的小技巧——它创建了一个新的`LiveWeatherService`实例并将其向上转换为`WeatherService`,这样做是成功的，因为对象符合接口(即使没有显式声明)。这也强制要求`LiveWeatherService`的`baseUri`实例成员是隐藏的(即私有的)，尽管流实际上并不支持类私有成员。(这在 TypeScript 中以同样的方式工作，但是通常人们在那里使用私有类成员。)

事实上，`LiveWeatherService`类本身是隐藏的——用户没有办法直接访问和实例化它。我们提供了一个简单的函数来实例化一个`WeatherService`对象，以解耦接口和实现。

## 抽象数据类型

`WeatherService`接口隐藏了*关于底层对象的一切*，除了我们可以用它做一件事，那就是调用它的`getForecast`方法。这实际上是抽象数据类型的本质——这些类型是根据您可以用它们做什么来定义的，而不是公开它们的内部形状。

不过，Flow/TS 在这一点上做了一个巧妙的转变，在某种程度上发挥了 JavaScript 的优势:它们让接口包含简单 JavaScript 对象属性的成员，这意味着它们还允许您准确地公开它们的内部形状。这在像 JavaScript 这样的语言中是非常宝贵的，对象可以以这种轻量级的方式创建和传递，并在许多不同的用途中派上用场，从编写查询 DSL 到向函数传递可选参数。

回到抽象数据类型(ADT)——当然，它们是我们理解如何大规模编程的[重大突破](https://youtu.be/w2PmXJL8_WM)之一。使用相互隐藏实现细节的类型、模块或类，我们能够实现编程的追求目标:解耦，即[切断依赖](https://www.tedinski.com/2018/07/31/interfaces-cutting-dependencies.html)。

## 可测性

这一点最明显的地方之一是在测试您的代码时。自动化测试是每一个设计良好的代码库的第一消费者，尤其是单元测试是代码模块化程度的一只金丝雀。如果它的模块化程度不够，不能单独测试其单元，那么在面对不可避免的变化时，它可能不够灵活和健壮。

单元测试的关键是“单元”部分——也就是说，孤立地测试一件事情。正如我提到的，接口允许你减少依赖，从而将代码片段相互隔离。

例如，假设您想测试一些使用上述`WeatherService`的代码。这段代码可能简单，也可能复杂。现在让我们假设根据今天的高温返回一条消息是合理的:

```
export async function weatherMessage(
  weatherService: WeatherService,
): Promise<string> {
  const [{highTemp}] = await weatherService.getForecast(
    /* city = */ 'Toronto',
    /* numDays = */ 1,
  )

  if (highTemp < 0) return 'Brr!'
  else if (highTemp < 20) return 'Chilly!'
  else if (highTemp < 30) return 'Noice!'
  else return 'Hot!'
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管在生产代码中触发了一个网络调用，但是这个函数很容易测试。它与`WeatherService`实现相隔离，不需要知道服务是如何用基本 URI 构建的，也不需要知道服务是如何进行网络调用的。它关心的只是得到符合服务接口的东西。

### 模仿

你可能已经猜到了它的发展方向:模仿。但是这里你可以使用*模仿*(作为名词)，而不是*模仿*(作为动词)这种有问题的做法。正如 [José Valim 推荐的](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)，我们可以用一个符合相同接口的模拟来替换真实的服务实现。例如:

```
type Responses = {|
  getForecast?: Forecast[][],
|}

export default function mockWeatherService(
  responses: Responses,
): WeatherService {
  return new MockWeatherService(responses)
}

class MockWeatherService {
  responses: Responses

  constructor(responses: Responses) {
    this.responses = responses
  }

  async getForecast(
    city: string,
    numDays: number,
    tempUnit?: TempUnit,
  ): Promise<Forecast[]> {
    return this.responses.getForecast ?
      this.responses.getForecast.shift() :
      unhandled({city, numDays, tempUnit})
  }
}

function unhandled<A>(args: Object): A {
  throw new Error(`Unhandled request with arguments: ${args}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，一个单元测试可以是这样的:

```
describe('weatherMessage', () =>
  it('works for sub-zero', () =>
    expect(weatherMessage(mockWeatherService({
      getForecast: [[{
        city: '',
        day: {year: 0, month: 0, day: 0},
        tempUnit: 'C',
        highTemp: -1,
        /* This obviously doesn't make sense but it doesn't matter for
           this test */
        lowTemp: 0,
      }]]
    }))).toMatchSnapshot()
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

快照应该是:`Brr!`。

这里发生了一些不同的事情:

*   我们建立了一个模拟天气服务，它由模拟响应(类型为`Responses`)构成。有趣的是，这是一个精确的对象类型，而不是一个接口，因为如果我们传入一个错误的响应名，我们希望出现一个编译错误。)
*   当`weatherMessage`触发服务调用时，它用响应来响应，将它们一个接一个地弹出响应数组
*   如果服务调用没有响应，模拟服务会抛出一个“未处理的请求”异常，这样测试就会失败

这个模拟使我们能够快速开始编写测试。我们可以从一个最小的失败测试开始:

```
expect(weatherMessage(mockWeatherService({})))
  .toMatchSnapshot() 
```

Enter fullscreen mode Exit fullscreen mode

...并得到一个“未处理的请求”异常，告诉我们接下来需要提供哪个响应模拟。如果你像这样设置了服务模拟，那么在一个 Express 应用中为控制器编写单元测试将会涉及到一系列的测试异常，这些异常将会*引导*你完成设置模拟响应的过程。您提供的响应将决定采用控制器逻辑的哪个分支，从而决定接下来将触发哪个服务调用(如果有的话)。可以想象，测试编写过程几乎是*交互式的。*

## 接口–好的零件

当然，接口不是灵丹妙药。你会注意到，即使在上面的例子中，我也没有在每个类型定义中使用它们。明智地使用，他们以我描述的方式包装相当多的冲击。但是它们有其局限性。

我认为我们在 JavaScript 世界中是幸运的，我们有类型系统让我们以一种(相对)安全的方式对 JavaScript 建模。他们完全正确的建模工具之一是接口。请记住，Flow/TS 类型是纯粹的编译时构造，它们会从输出 JavaScript 中删除。有了这么低的语法和运行时成本，接口和其他类型让我们在为用户提供功能的同时，管理复杂性并实现质量。