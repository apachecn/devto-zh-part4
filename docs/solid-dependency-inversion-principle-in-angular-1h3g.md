# 立体:角度中的从属反转原理

> 原文：<https://dev.to/philipszdavido/solid-dependency-inversion-principle-in-angular-1h3g>

[![](img/6dc5220645eb23fbda0e56e93a8b4211.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rWIYhgvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2A_0Qt3IrDReUYROo387OIVg.jpeg)

> 帖子最初发布于 [blog.bitsrc.io](https://blog.bitsrc.io/solid-the-dependency-inversion-principle-in-angular-6e4b9c484960?source=---------6------------------)
> 
> # *A .高层模块不应该依赖低层模块。两者都应该依赖于抽象。*
> 
> # *B .抽象不应该依赖细节。细节应该依赖于抽象。*

这个原则声明类和模块应该依赖于抽象而不是具体化。

> # *The principle of dependency inversion (DIP) tells us that the most flexible systems are those whose source code dependency only refers to the abstract rather than the concrete.*

### 提示:使用 Bit 来充分利用您的立体角项目

实体代码是模块化的，可重用的。有了[* *位](https://bit.dev)，**你就可以轻松* *共享和组织你的可重用组件。**让您的团队看到您所做的工作，跨项目安装和重用您的组件，甚至在单个组件上进行协作。[试试看](https://bit.dev)。
[**共享可重用代码组件作为一个团队 Bit**
*轻松地在项目和应用程序之间共享可重用组件，以更快地构建一个团队。合作开发…*bit.dev](https://bit.dev)

什么是抽象？

抽象是接口。接口定义了实现类必须具备的东西。如果我们吃界面餐:

```
interface Meal {
    type: string
} 
```

它保存了提供什么类型膳食的信息；早餐、午餐或晚餐。像早餐、午餐和晚餐这样的实现类必须具有 type 属性:

```
class BreakFastMeal implements Meal {
    type: string = "Breakfast"
}

class LunchMeal implements Meal {
    type: string = "Lunch"
}

class DinnerMeal implements Meal {
    type: string = "Dinner"
} 
```

所以，你可以看到 Interface 给出了实现它的类必须拥有什么属性和方法的信息。接口之所以被称为抽象，是因为它关注的是一个类的特征，而不是这个类的一组特征。

什么是结核？

具体化是类。它们是抽象的对立面，它们包含了其特征的完整实现。上面我们说了接口 Meal 是一个抽象，那么实现它的类，正餐、早餐和午餐是具体化，因为它们包含了 Meal 接口的完整实现。饭有特色类型说应该是串串类型，然后早餐饭来了说类型是“早餐”，午餐饭说类型是“午餐”。

DIP 说，如果我们依赖于具体化，它将使我们的类或模块与细节紧密耦合。组件之间的耦合导致了一个僵化的系统，它很难改变，并且在引入改变时会失败。

## 例如:复印机

让我们用一个例子来说明使用 DIP 的效果。假设我们有一个程序，它从磁盘获取输入，并将内容复制到闪存驱动器。

该程序将从磁盘中读取一个字符，并将其传递给将它写入闪存驱动器的模块。

源代码将如下所示:

```
function Copy() {
    let bytes = []
    while(ReadFromDisk(bytes))
        WriteToFlashDrv(bytes)
} 
```

是的，这是一项出色的工作，但这个系统是僵化的，而不是灵活的。系统仅限于从磁盘读取和向闪存驱动器写入。当客户端希望从磁盘读取数据并写入网络时会发生什么情况？我们将看到我们自己添加一个 if 语句来支持新添加的内容

```
function Copy(to) {
    let bytes = []
    while(ReadFromDisk(bytes))
        if(to == To.Net)
            WriteToNet(bytes)
        else
            WriteToFlashDrv(bytes)
} 
```

看我们*摸了*代码，不应该这样。随着时间的推移，越来越多的设备必须参与复制程序，复制功能将充满 if/else 语句，并将依赖于许多较低级别的模块。最终会变得僵硬脆弱。

为了使复制函数可重用且不那么脆弱，我们将实现接口 writer 和 Reader，这样我们想要读取的任何地方都将实现 Reader 接口，我们想要写入的任何地方都将实现 Write 接口:

```
interface Writer {
    write(bytes)
}

interface Reader {
    read(bytes)
} 
```

现在，我们的磁盘读取器将实现读取器接口:

```
class DiskReader implements Reader {
    read(bytes) {
        //.. implementation here
    }
} 
```

然后，网络编写器和闪存驱动器编写器都将实现编写器接口:

```
class Network implements Writer {
    write(bytes) {
        // network implementation here
    }
}

class FlashDrv implements Writer {
    write(bytes) {
        // flash drive implementation
    }
} 
```

复制功能应该是这样的:

```
function Copy(to) {
    let bytes = []
    while(ReadFromDisk(bytes))
        if(to == To.Net)
            WriteToNet(bytes)
        else
            WriteToFlashDrv(bytes)
}

|
|
v

function Copy(writer: Writer, reader: Reader) {
    let bytes = []
    while(reader.read(bytes))
        writer.write(bytes)
} 
```

看，我们的拷贝被缩短成几个代码。Copy 函数现在依赖于接口，它所知道的是 Reader 会调用 read 方法来写入字节，Reader 会调用 read 方法来获取要写入的字节，它不关心如何获取数据，这是实现 Writer 的类的责任。

这使得复制功能具有高度的可重用性，并且不那么脆弱。我们可以将任何读者或作者传递给复制函数，它所关心的是:

```
// read from disk and write to flash drive
Copy(FlasDrvWriter, DiskReader)

// read from flash and write to disk
Copy(DiskWriter, FlasDrvReader)

// read from disk and write to network ie. uploading
Copy(NetworkWriter, DiskReader)

// read from network and write to disk ie. downloading
Copy(DiskWriter, NetworkReader) 
```

## 示例:Nodejs 控制台类

Nodejs 控制台类是服从 DIP 的真实应用程序的一个例子。控制台类产生输出，是的，主要用于输出到终端，但它也可以用于输出到其他媒体，如:

*   文件

*   网络

当我们做 console.log("Nnamdi ")

Nnamdi 被打印到屏幕上，我们可以像上面概述的那样将输出引导到另一个地方。

查看控制台类

```
function Console(stdout, stderr) {
    this.stdout = stdout
    this.stderr = stderr ? stderr : stdout
}

Console.prototype.log = function (whatToWrite) {
    this.write(whatToWrite, this.stdout)
}

Console.prototype.error = function (whatToWrite) {
    this.write(whatToWrite, this.stderr)
}

Console.prototype.write = function (whatToWrite, stream) {
    stream.write(whatToWrite)
} 
```

它接受流形式的 stdout 和 stderr，它们是通用的，流可以是终端、文件或任何类似网络的流。stdout 是写错误的地方，stderr 是写错误的地方。我们全局拥有的控制台对象已经用要写入终端的流集进行了初始化:

```
global.console = new Console(process.stdout, process.stderr) 
```

stdout 和 stderr 是具有 write 方法的接口，控制台只知道调用 stdout 和 stderr 的 write 方法。

控制台依赖于抽象的 stdout 和 stderr，由用户提供输出流，并且必须有 write 方法。

要使控制台类写入文件，我们只需创建一个文件流:

```
const fsStream = fs.createWritestream('./log.log') 
```

我们的文件是 log.log，我们使用 fs 的 createWriteStream API 创建了一个可写的流。

我们可以创建另一个流来记录我们的错误报告:

```
const errfsStream = fs.createWritestream('./error.log') 
```

我们现在可以将这两个流传递给控制台类:

```
const log = new Console(fsStream, errfsStream) 
```

当我们调用 log.log("将输入记录到。/log.log ")，它不会将其打印到屏幕上，而是将消息写入。/log.log 文件。

很简单，控制台不需要一长串的 if/else 语句来支持任何流。

## 棱角分明

来到 Angular 我们如何服从倾角？

假设我们有一个计费应用程序，列出人们的许可证并计算他们的费用，我们的应用程序可能如下所示:

```
@Component({
    template: `
        <div>
            <h3>License</h3>
            <div *ngFor="let p of people">
                <p>Name: {{p.name}}</p>
                <p>License: {{p.licenseType}}</p>
                <p>Fee: {{calculateFee(p)}}</p>
            </div>
        </div>    
    `
})
export class App {
    people = [
        {
            name: 'Nnamdi',
            licenseType: 'personal'
        },
        {
            name: 'John',
            licenseType: 'buisness'
        },
        // ...
    ]

    constructor(private licenseService: LicenseService) {}

    calculateLicenseFee(p) {
        return this.licenseService.calculateFee(p)        
    }
} 
```

我们有一项根据许可证计算费用的服务:

```
@Injectable()
export class LicenseService {
    calculateFee(data) {
        if(data.licenseType == "personal")
             //... calculate fee based on "personal" licnese type
        else
         //... calculate fee based on "buisness" licnese type
    }
} 
```

此服务类别违反了 DIP，当引入另一个许可证类型时，我们将看到自己添加另一个 if 语句分支来支持新添加的内容:

```
@Injectable()
export class LicenseService {
    calculateFee(data) {
        if(data.licenseType == "personal")
             //... calculate fee based on "personal" licnese type
        else if(data.licenseType == "new license type")
            //... calculate the fee based on "new license type" license type
        else
            //... calculate fee based on "buisness" licnese type
    }
} 
```

为了使它服从 DIP，我们将创建一个许可接口:

```
interface License {
    calcFee():
} 
```

然后我们可以有实现它的类，比如:

```
class PersonalLicense implements License {
    calcFee() {
        //... calculate fee based on "personal" licnese type
    }
    // ... other methods and properties
}

class BuisnessLicense implements License {
    calcFee() {
        //... calculate fee based on "buisness" licnese type
    }
    // ... other methods and properties
} 
```

然后，我们将重构 LicenseService 类:

```
@Injectable()
export class LicenseService {
    calculateFee(data: License) {
        return data.calcFee()
    }
} 
```

它接受许可证类型的数据，现在我们可以向 LicenseService # calculateFee 发送任何许可证类型，它不关心许可证的类型，它只知道数据是许可证类型，并调用它的 calcFee 方法。实现许可证接口的类在 calcFee 方法中提供许可证费用计算。

角度本身也服从倾斜，在它的源头。例如在管道概念中。

## 管道

管道用于在不影响源的情况下转换数据。在阵列中，我们像这样转换数据:

*   绘图

*   过滤

*   整理

*   插接

*   限幅

*   子串*眨眼表情符号此处*

*   等等

所有这些都基于实现来转换数据。

在 Angular 模板中，如果我们没有管道接口，我们就会有转换数据管道的类，如数字、日期、JSON 或自定义管道等。Angular 的管道实现如下:

```
pipe(pipeInstance) {
    if (pipeInstance.type == 'number')
        // transform number
    if(pipeInstance.type == 'date')
        // transform date
} 
```

如果 Angular 添加新管道，该列表将会扩展，并且支持自定义管道将会更加困难。

所以 Angular 创建了一个所有管道都会实现的 PipeTransform 接口:

```
interface PipeTransform {
    transform(data: any)
} 
```

现在，任何管道都将实现该接口，并在转换方法中提供其管道功能/算法。

```
@Pipe(...)
class NumberPipe implements PipeTransform {
    transform(num: any) {
        // ...
    }
}

@Pipe(...)
class DatePipe implements PipeTransform {
    transform(date: any) {
        // ...
    }
}

@Pipe(...)
class JsonPipe implements PipeTransform {
    transform(jsonData: any) {
        // ...
    }
} 
```

现在，Angular 将调用转换，而不关心管道的类型

```
function pipe(pipeInstance: PipeTransform, data: any) {

    return pipeInstance.transform(data)

} 
```

## 
 [T3】
结论](#conclusion) 

在这篇文章中，我们看到了 DIP 是如何让我们整体上用 Angular 和 OOP 编写可重用和可维护的代码的。

在 ***工程笔记本的 C++报告栏中*为**在*的依赖倒置原则*栏中，它写道:

一个软件满足了它的需求，却表现出以下三个特征中的任何一个或全部，这是一个糟糕的设计。

1.  很难改变，因为每一个改变都会影响到系统中太多的其他部分。(刚性)

2.  当您做出更改时，系统中意想不到的部分会中断。(脆弱性)

3.  很难在另一个应用程序中重用，因为它无法从当前应用程序中分离出来。(不动)

如果你对此有任何问题，或者我应该添加，纠正或删除的任何内容，请随时评论，发电子邮件或发短信给我

谢谢！！！

## 了解更多

[**如何在项目和应用之间共享 Angular 组件**
*跨项目共享和协作 NG 组件，更快地构建您的应用。*blog.bitsrc.io](https://blog.bitsrc.io/how-to-share-angular-components-between-project-and-apps-5eb0600d99d2)
[**宣布与 Angular 公开测试版**
*特别感谢出色的 Angular 团队共同努力实现这一目标👐*blog.bitsrc.io](https://blog.bitsrc.io/announcing-bit-with-angular-public-beta-578cbb173690)