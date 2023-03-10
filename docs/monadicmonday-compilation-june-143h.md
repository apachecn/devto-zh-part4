# #MonadicMonday 编译:六月

> 原文：<https://dev.to/ybogomolov/monadicmonday-compilation-june-143h>

最近，我在 Twitter 上发起了一个名为#monadicmonday 的小活动——每周一我都会发布一个帖子，介绍一些有用的、易于立即使用的 FP 内容。每个月我都会为那些喜欢读长篇文章的人做一个汇编。

# 第十集:选择 FP 的理由

欢迎收看#monadicmonday 第十集！我想让今天的节目变得简洁和无代码，并谈谈选择 FP 的原因，以及如何向客户展示它的可行性。

当你作为一名工程师或架构师做决定时，你必须考虑很多事情。功能方法远不如命令式风格受欢迎，这不是什么大秘密，当你选择它时，你必须处理后果。

这些后果会是什么？

1.  首先，增加了开发人员入职的复杂性。除非你把所有函数性的东西藏在某个地方，否则你的开发者必须学习什么是函子、单子、应用程序等等。以及如何使用它们。即使这样也不能保证您的代码库将遵循最佳的功能实践。仍然有可能使用单子&stuff 来写命令式，并且认为你在“做 FP”。
2.  第二，如果你想从市场上招聘已经熟悉 FP devs 的人，准备好关闭你的空缺职位几个月，除非你的主要语言是(半)纯功能语言，比如 Haskell/pure script/等。如果你把核心部分(比如递归模式或更自由的单子)暴露在表面，很难找到一个真正理解这些主题的人。
3.  第三，关于计划生育的实用材料的数量远远少于任何其他方法。是的，我们有很多“另一个单子”教程，但是几乎没有关于功能设计模式、心智模型和问题分解例子的材料。如今情况正在慢慢改变，我希望站在那些最终彻底改变它的人中间。

但是，当我们决定用函数式范式编写时，我们得到了什么好处呢？

1.  等式推理。当我们用纯函数、单子、代数数据类型编程时，我们获得了等式推理的巨大能力——在所有上下文中用等号代替等号的能力。这允许 ut 检查我们程序的正确性，并对我们对代码的理解有信心。我必须承认，这需要额外的努力，在许多情况下还需要语言的支持。
2.  模块化和可组合性。这也可以在其他风格中实现，比如说，在传统的 OOP 中。低耦合和高内聚要求您的代码是模块化和隔离的，这通常会导致良好的可组合性。然而，只有在函数式风格中，代码的这些特征才是整个方法的基石。
3.  可测试性。如果你的代码是模块化的，模仿它的一部分并独立测试它们是非常容易的。请查看关于无标签决赛的第五集和以那种风格测试程序的例子:[https://Twitter . com/YuriyBogomolov/status/112424215620341760](https://twitter.com/YuriyBogomolov/status/1124244215620341760)
4.  鲁棒性。我指的是数学意义上的健壮性——如果你的代码遵循结合性、传递性、幂等性、总体性等数学法则，它包含错误的可能性就小得多。这并不意味着你不需要测试或者你可以写得很潦草，但是当编译器支持你的时候，这是一种很好的感觉。
5.  正确性的证明。我在这里看到了一个地平线，但是使用类型理论的数学工具和它的后继者(比如 HoTT ),有可能建立你正在建立的系统的正确性的自动证明。使用 Coq 和 Agda 这样的独立类型语言，不仅可以表达应用程序的逻辑，还可以证明它不包含缺陷和错误。Dep 类型的语言要在生产中真正有用还有很长的路要走，但这仅仅是开始。

那么，当你为自己使用 FP 的决定辩护时，你该如何与客户沟通呢？首先，您必须回答一个简单的问题:该客户面临的主要难题是什么？他/她的代码是紧耦合的，可测试性差吗？是不是复用性不高？它有很多运行时错误吗，比如 NPE 或者‘未定义不是函数’？对这些棘手问题进行排序，并围绕这些问题的解决方案构建您的陈述。此外，对自己要公平，不要仅仅为了带来 FP 而带来 FP:)总是要考虑你将要一起工作的人，代码库的整体情况以及你将要工作的环境。与一个在所有解决方案中使用 JavaEE 超过 20 年的客户谈论 FP 和迁移到 Scala 并不是一个好主意。但是，如果您正在构建一个前端应用程序，您可以选择 TypeScript——并通过向客户展示这可以减少每行的错误数量，为纯 JS 开发人员提供更多的开发便利和轻松的过渡，来说服客户这是一个可行的决定。几乎在任何语言中，都有可能使用一些 FP，在你的解决方案中找到 FP 和非 FP 的平衡取决于你。

加入讨论！我很想听听您向客户“销售”FP 的经验、学到的教训和过渡方法。

# 第 11 集:克雷斯利之箭

欢迎收看#monadicmonday 第十一集！今天我们就来聊聊 Kleisli arrows，我来介绍一下我的小项目叫做“kleisli-ts”。

函数组合是函数式编程的基石。有了`f :: A -> B`和`g :: B -> C`，我们组成`g . f`，得到`h :: A -> C`。当我们讨论全纯函数的时候，这很有效。但是当我们与现实世界互动时，问题就出现了。Real World 是异步的、不纯的，似乎与我们想要的完全相反。

通常的方法包括单子的所有荣耀——IO、Task、Reader，其顶点是双功能 ZIO(见[https://Twitter . com/YuriyBogomolov/status/1125403292530507776](https://twitter.com/YuriyBogomolov/status/1125403292530507776))。或许，你知道单子的主要问题——它们不组合，至少在一般情况下是这样的:【https://www.slideshare.net/pjschwarz/monads-do-not-compose

然而，还有另一种方法来编写有效的函数，这种方法包括 Kleisli arrows。

克莱斯利箭头是以瑞士数学家海因里希·克莱斯利的名字命名的，他从事范畴论和同伦论的研究。这些箭头是形式为`f :: A -> F B`的函数，将`F`作为我们计算的上下文。在 TypeScript 中，我们可以把它们写成一个有三个参数的类型，用 fp-ts 符号表示 HKTs:

```
// should be interpreted as: (a: A) => F<B>
type Kleisli<F extends URIS, A, B> = (a: A) => Type<F, B>; 
```

Enter fullscreen mode Exit fullscreen mode

有了这种形式的函数，如果我们有一个`F`类的单子(或者更窄:如果我们有一个链的话):
，我们就可以组合它们

```
const composeK = <F extends URIS>(F: Chain1<F>) =>
  <A, B, C>(f: Kleisli<F, A, B>, g: Kleisli<F, B, C>): Kleisli<F, A, C> => 
    (a: A) => F.chain(f(a), g); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以定义一些有用的组合子，比如`zipWith`用于压缩两个 Kleisli 箭头，或者`ifThenElse`用于组织条件流:

```
const zipWith = <F extends URIS>(M: Chain1<F>) =>
  <A, B, C, D>(l: Kleisli<F, A, B>, r: Kleisli<F, A, C>) =>
    (f: (t: [B, C]) => D): Kleisli<F, A, D> =>
      (a) => M.chain(l(a), (b) => M.map(r(a), (c) => f([b, c])));

const ifThenElse = <F extends URIS>(M: Chain1<F>) =>
  <A, B>(cond: Kleisli<F, A, boolean>) =>
    (then: Kleisli<F, A, B>) => (else_: Kleisli<F, A, B>): Kleisli<F, A, B> =>
      (a) => M.chain(cond(a), (t) => t ? then(a) : else_(a)); 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来像是半开玩笑的替换——毕竟，我们仍然使用一元链接来传递值——但实际上，这种方法允许您以不同的方式思考代码，更重要的是，Kleisli 箭头允许进行一些优化，减轻一元包装器的分配开销。

我要感谢@jdegoes 在 LambdaConf'18 上关于 KleisliIO 的精彩演讲:[https://www.youtube.com/watch?v=L8AEj6IRNEE](https://www.youtube.com/watch?v=L8AEj6IRNEE)。您可以在那里找到基准和一些实现细节。我更进一步，在约翰的同意下，将 KleisliIO 移植到 TypeScript，并做了一些小的改进，比如 Bifunctor 实例:【https://github.com/YBogomolov/kleisli-ts

我应该承认，在使用了一段时间的 bifunctor IO(FP-ts 中的 IO 要么，task 要么，Scala 中的 ZIO)之后，我发现它的表现力比普通的 ol' IO 

让我们看看例子。如果你还记得第五集和第六集，我们用 Tagless Final 和 ZIO/TIO 编写了一个随机生成应用程序。现在，我们可以使用另一种方法来编写它——使用 KleisliIO。

我们首先为给定的单子获取 KleisliIO API 的一个实例，在本例中是——task 要么:

```
import { taskEither, tryCatch, URI } from 'fp-ts/lib/TaskEither';
import { getInstancesFor, KleisliIO } from 'kleisli-ts/lib';

const K = getInstancesFor(taskEither); 
```

Enter fullscreen mode Exit fullscreen mode

这里与众不同的部分是，我们通过将函数组合成一个整体来描述我们的计算，而不是分别看它们的输入和输出:

```
// Note usage of `ifThenElse` combinator here:
export const parse: KleisliIO<URI, Error, string, number> =
  K.ifThenElse<Error, string, number>
    (K.liftK((s: string) => {
      const i = +s;
      return isNaN(i) || i % 1 !== 0;
    }))
    (K.identity<Error, string>().chain((s) => K.fail(new Error(s + ' is not a number'))))
    (K.liftK(Number));

// We usually lift impure functions into Kleisli arrows via `liftK` or `impure`/`impureVoid`:
export const generateRandom: KleisliIO<URI, never, number, number> =
  K.impureVoid((lowerBound) => Math.floor(Math.random() * lowerBound + 1));

export const print: KleisliIO<URI, never, string, void> =
  K.impureVoid((message: string) => console.log(message)); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在这里必须处理异步 I/O，所以我们可以利用方便的`pure`方法，它与纯一元计算一起工作:

```
import { createInterface } from 'readline';

export const read: KleisliIO<URI, Error, void, string> =
  K.pure(
    () => tryCatch(() => new Promise<string>((resolve) => {
      const rl = createInterface({
        input: process.stdin,
        output: process.stdout,
      });
      rl.question('> ', (answer) => {
        rl.close();
        resolve(answer);
      });
    }), (e) => new Error(String(e))),
  ); 
```

Enter fullscreen mode Exit fullscreen mode

现在有趣的部分是:我们可以使用像`andThen`或`chain`这样的链接组合子将我们的函数连接在一起。与用`compose`组合纯全函数相比！

```
export const getUpperStr: KleisliIO<URI, Error, void, string> =
  K.of<Error, void, string>('Enter random upper bound:')
    .andThen(print)
    .andThen(read);

export const checkContinue: KleisliIO<URI, Error, void, boolean> =
  K.of<Error, void, string>('Do you want to continue?')
    .andThen(print)
    .andThen(read)
    .chain((answer) => {
      switch (answer.toLowerCase()) {
        case 'y':
          return K.of(true);
        case 'n':
          return K.of(false);
        default:
          return checkContinue;
      }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

最后我们可以编写并运行我们的主程序:

```
import { unsafeRunTE } from 'kleisli-ts/lib/unsafe';

export const main: KleisliIO<URI, Error, void, void> =
  getUpperStr
    .andThen(parse)
    .andThen(generateRandom)
    .chain((rnd) => K.of<Error, void, string>(`Your random is: ${rnd}`).andThen(print))
    .andThen(checkContinue)
    .chain((answer) => answer ? main : K.of<Error, void, string>('Good-bye').andThen(print));

unsafeRunTE(main.run()); 
```

Enter fullscreen mode Exit fullscreen mode

这就结束了我对克莱斯利箭的介绍。

就这些了，伙计们。希望你喜欢这一集！并敬请期待七月编及下集；)
像往常一样，所有代码示例都可以在[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)获得