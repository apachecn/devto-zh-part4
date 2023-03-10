# #MonadicMonday 编译:七月

> 原文：<https://dev.to/ybogomolov/monadicmonday-compilation-july-4pal>

最近，我在 Twitter 上发起了一个名为#monadicmonday 的小活动——每周一我都会发布一个帖子，介绍一些有用的、易于立即使用的 FP 内容。每个月我都会为那些喜欢读长篇文章的人做一个汇编。

# 第 12 集:期货

欢迎收看#monadicmonday 第十二集！今天，我想谈谈未来——异步懒惰任务的概念，它是热切承诺的更好替代物。由于它们的懒惰，futures 支持取消、竞赛、并行和开箱即用的资源安全。我见过最好的用于期货的库是 [fluture-js](https://github.com/fluture-js/Fluture) 。

有趣的事实:在 Scala 世界中,`Future`不是单子，而是类似于 JS 的`Promise`的东西——也就是说，渴望引用的非透明构造。相反，使用 IO (cats)、Task (monix & scalaz)或 ZIO (zio)来实现今天主题所描述的好处要好得多。

在`fp-ts` &静态术语中，未来可以被描述为任务>。但是这比我的简化所暗示的要多得多。所以让我们开始吧！

你有很多创造未来的方法。有关方法的完整列表，请参见[https://github . com/Fluture-js/Fluture/tree/11 . x # creating-futures](https://github.com/fluture-js/Fluture/tree/11.x#creating-futures)
请注意，由于 TS 的类型推断方式，我必须手动提供类型参数:

```
import * as F from 'fluture';

const f1 = F.of<Error, number>(42);
const f2 = F.node<Error, Buffer>((done) => fs.readFile('./foo.txt', done));
const f3 = F.attempt<Error, void>(() => fs.writeFileSync('./bar.txt', 'Hello!'));
const f4 = F.tryP<Error, string>(async () => {
  const file = await fs.promises.readFile('./foo.txt');
  const text = file.toString();
  await fetch('https://example.com', { method: 'POST', body: text });
  return text;
});
const f5 = F.after<Error, number>(500, 42); 
```

Enter fullscreen mode Exit fullscreen mode

为什么未来是光明而凉爽的？首先，它是一个纯值——也就是说，未来不会运行，直到你调用`fork`或`promise`方法:

```
const f3 = F.attempt(() => fs.writeFileSync('./bar.txt', 'Hello!')); // will not create a file...
f3.promise(); // ...up until this time!
f3.fork(console.error, console.log); // ...or this time :) 
```

Enter fullscreen mode Exit fullscreen mode

第二，期货支持取消，使超时请求变得轻而易举:

```
const fetchF = F.encaseP(fetch); // convert `fetch` into a Future-returning function

const getUserLogin = fetchF('https://api.github.com/users/YBogomolov')
  .chain((res) => F.tryP(() => res.json()))
  .map<string>((user) => user.login);

getUserLogin
  // all we need to do for introducing a timeout:
  .race(F.rejectAfter<Error, string>(1000)(new Error('timeout')))
  .fork(console.error, console.log); 
```

Enter fullscreen mode Exit fullscreen mode

第三，期货是安全的:

```
const add100000 = function self(x: number): F.FutureInstance<never, number> {
  const mx = F.of<never, number>(x + 1);
  return x < 100000 ? mx.chain(self) : mx;
};

add100000(1).fork(console.error, console.log); // => 100001 
```

Enter fullscreen mode Exit fullscreen mode

第四，期货是一元、二元和替代物，所以你可以使用所有你曾经用过的机器:

```
getUserLogin // let's pretend we get a login somehow...
  .alt(F.of('admin')) // ...or fall back to 'admin'
  // then let's search for that user:
  .chain((login) => fetchF(`https://api.github.com/search/users?q=${login}`).chain((res) => F.tryP(() => res.json())))
  .chain((users) => users.total_count > 0 ? F.of(users.items[0]) : F.reject(new Error('not found')))
  // ...and get his/her repositories:
  .chain((user) => fetchF(user.repos_url).chain((res) => F.tryP(() => res.json())))
  // ...and simplify our container, throwing away redundant info:
  .bimap((reason: Error) => reason.toString(), (repos) => repos.length)
  .fork(console.error, console.log); // => 30 
```

Enter fullscreen mode Exit fullscreen mode

最后，`fluture`有一个不错的风格——这是对哈斯克尔的`do`符号的一种模仿，可以通过名字`do`和`go`获得。顺便提一下，我必须承认为结果编写类型的尝试非常麻烦，但是 ts 可以完美地推断出结果:

```
const calc200002 = F.go(function*() {
  const res: number = yield add100000(1);
  const res2: number = yield add100000(1);
  return res + res2;
});

calc200002.fork(console.error, console.log); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，期货是一个非常强大的功能概念，使用它们真的很容易，这要感谢令人敬畏的`fluture-js`！我鼓励您阅读它的文档和示例，并在您的项目中尝试它——它经过了战斗的考验，具有很高的性能:[https://github.com/fluture-js/Fluture/tree/11.x](https://github.com/fluture-js/Fluture/tree/11.x)

我对未来编程的简短介绍到此结束。像往常一样，所有代码示例都可以在[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)获得

# 第十三集:基于属性的测试

欢迎收看#monadicmonday 第十三集！今天我们将讨论一下使用令人敬畏的库`fast-check`进行基于属性的测试。

当你写单元测试时，你通常会这样做:

```
import { expect } from 'chai';
import { left, right } from 'fp-ts/lib/Either';

import f from './function-to-test';

it('should return a Right<string> for given number', () => {
  const mockInput = 42;
  const mockOutput = 'foobar';

  expect(f(mockInput)).to.deep.equal(right(mockOutput));
});

it('should return a Left<Error> if input is not a number', () => {
  const mockInput = 'aaaa';
  f(mockInput).fold(
    (e) => expect(e)
      .to.be.an.instanceOf(Error)
      .and
      .to.have.property('message').include(mockInput),
    () => expect.fail('should not be Right'),
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

然而，这仅仅是测试你的模块的逻辑。你必须编写大量的单元测试来覆盖所有的边界，处理可能的类型错误等等。例如，有多少说英语的程序员测试他们的字符串接受函数来接受西里尔字母或汉字输入？

如果你写了一些测试，你就会知道为你的模拟准备好测试集有多难。通常，您最终会得到定制的模拟生成器或一组包含预生成数据的巨大 JSONs。但还是有可能你错过了什么。

所以基于属性的测试来拯救。它的思想相当简单:我们断言被测试算法的某些属性成立，测试框架生成样本随机数据来证明或否定这一断言。

对于 TypeScript 和 JavaScript，有一个很好的库叫做`fast-check`:[https://github.com/dubzzz/fast-check](https://github.com/dubzzz/fast-check)。它与 mocha、jest、ava、tape 和 jasmine 集成得很好。让我们看看如何编写基于属性的测试。

我们从一个相当简单的测试开始:“检查任何字符串是否包含自身”。类似于字符串的同一性:

```
import * as fc from 'fast-check';

describe('Identity laws for string', () => {
  it('any Unicode string should contain itself', () => {
  //runner:   property:   arbitrary:              actual assertion:
    fc.assert(fc.property(fc.fullUnicodeString(), (str) => str.includes(str)));
  });

  // this one has faulty logic, so it should fail and provide you with a counterexample:
  it('should fail with counterexample', () => {
    fc.assert(fc.property(fc.fullUnicodeString(), (str) => str.trim().includes(str)));
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行这些测试将为您提供一个错误逻辑的反例，因此您可以使用它来调试测试主题并修复 bug:

```
> jest ./src/episode-13

 FAIL  src/episode-13/fc.test.ts
  ● Property tests › Identity laws for string › should fail with counterexample

    Property failed after 17 tests
    { seed: -976548053, path: "16", endOnFailure: true }
    Counterexample: [" "]
    Shrunk 0 time(s)
    Got error: Property failed by returning false

    Hint: Enable verbose mode in order to have the list of all failing values encountered during the run

       9 | 
      10 |     it('should fail with counterexample', () => {
    > 11 |       fc.assert(fc.property(fc.fullUnicodeString(), (str) => str.trim().includes(str)));
         |          ^
      12 |     });
      13 |   });
      14 | 
```

Enter fullscreen mode Exit fullscreen mode

如果不是有一些单子，我不会把`fast-check`库包括在单子星期一的节目中:)
仲裁实际上是单子，所以你可以使用它们的顺序属性来链接几个仲裁并构建一个复合对象。

让我们为这个程序建立一个属性测试:“如果两个用户都大于 18 岁，并且有一些共同的喜欢，那么将他们配对。否则，报告发现错误”。
实施将非常简单:

```
type Like = 'cars' | 'cats' | 'football';

interface User {
  login: string;
  age: number;
  email: string;
  likes: Like[];
}

const validate = (user: User): Either<Error, User> =>
  user.age < 18 ? left(new Error(`User ${user.login} must be over 18`)) : right(user);

const match = (user1: User, user2: User): Either<Error, [User, User]> => {
  // if both users have at least something in common:
  if (user1.likes.some((like) => user2.likes.includes(like))) {
    return right([user1, user2]);
  }

  return left(new Error(`No common likes for ${user1.login} and ${user2.login}`));
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们从编写一个验证测试开始。首先，我们需要为`User`类型:
定制一个任意生成器

```
const userArbitrary = fc.unicodeString().chain(
  // any Unicode login:
  (login) => fc.integer().chain(
    // any age – from -2^32 to 2^32, you'll see why in a moment:
    (age) => fc.emailAddress().chain(
      // any email address:
      (email) => fc.subarray<Like>(['cars', 'cats', 'football']).map<User>(
        // and `likes` could only contain `Like` type:
        (likes) => ({ login, age, email, likes }),
      ),
    ),
  ),
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以检查`validate`函数是否只传递给有效用户，并报告无效用户的年龄错误:

```
it('should validate a user', () => {
  fc.assert(fc.property(userArbitrary, (user) => validate(user).fold(
    (e) => {
      expect(e).to.be.an.instanceOf(Error).and.to.have.property('message').include(user.login);
    },
    (validatedUser) => {
      expect(validatedUser).to.deep.equal(user);
    },
  )));
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，由于`userArbitrary`能够生成任何整数年龄的用户，我们可以在所有可能的值上测试`validate`。
现在我们可以为`match`函数编写一个测试，使用前置条件来过滤套利:

```
it('should match two valid users', () => {
  fc.assert(fc.property(userArbitrary, userArbitrary, (user1, user2) => {
    fc.pre(user1.age >= 18 && user2.age >= 18);

    array.sequence(either)([validate(user1), validate(user2)]).fold(
      (vErr) => {
        expect(vErr.message.includes(user1.login) || vErr.message.includes(user2.login)).to.be.true;
      },
      ([u1, u2]) => match(u1, u2).fold(
        (pairError) => {
          expect(pairError.message.includes(user1.login) || pairError.message.includes(user2.login)).to.be.true;
        },
        (pair) => {
          expect(pair[0]).to.deep.equal(u1).and.deep.equal(user1);
          expect(pair[1]).to.deep.equal(u2).and.deep.equal(user2);
        },
      ),
    );
  }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

基于属性的测试的真正亮点是当你需要检查你的算法是否符合代数法则时——比如函子或单子的法则。
比如说，我们想要确保`Either`拥有函子法则。请注意，我生成了两个随机函数，并验证了它们的组成保持不变:

```
import * as fc from 'fast-check';
import { left, right } from 'fp-ts/lib/Either';
import { compose, identity } from 'fp-ts/lib/function';

describe('Either', () => {
  describe('Functor laws', () => {
    it('should preserve identity morphism', () => {
      // for any `x`, `either(x) map id` is isomorphic to `either(x)`:
      fc.assert(fc.property(fc.anything(), (x) => {
        expect(right(x).map(identity)).toEqual(right(x));
        expect(left(x).map(identity)).toEqual(left(x));
      }));
    });

    // for any `x`, `either(x) map f map g` is isomorphic to `either(x) map (g ∘ f)`:
    it('should preserve composition of morphisms', () => {
      fc.assert(fc.property(fc.anything(), fc.func(fc.anything()), fc.func(fc.anything()), (x, f, g) => {
        const g_o_f = compose(g, f);

        expect(right(x).map(f).map(g)).toEqual(right(x).map(g_o_f));
        expect(left(x).map(f).map(g)).toEqual(left(x).map(g_o_f));
      }));
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，你有了一个使用`fast-check`([https://github.com/dubzzz/fast-check](https://github.com/dubzzz/fast-check))的基于属性的测试的简要介绍。我鼓励您阅读它的文档，这些文档既全面又写得很好。

我对基于属性的测试的简短介绍到此结束。像往常一样，所有代码示例都可以在[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)获得

# 第 14 集:决赛

欢迎收看#monadicmonday 第十四集！今天的节目会很短，因为我决定现在就结束这个系列。

首先，我要感谢我所有的读者和关注者——你们的反馈和支持非常好，我真的很惊讶有这么多人用赞和转发来支持我的努力。

我希望《一元星期一》是一个实用的小故事，讲述有用的一元式结构、技巧和窍门，你可以在日常编码中使用它们。这些意图意味着我需要专注于非常简单的事情。另一方面，我不想从头开始描述什么是 Monad，什么是 Applicative，或者“折叠”什么意思…最后，我被 TypeScript 的类型系统所限制，与 v2 相比，v3 中的类型系统被严重削弱了。

所以基本上到目前为止，我已经涵盖了几乎所有我想要的主题，并且可以用打字稿来表达。我陷入了写作的瓶颈，需要一些时间来恢复。我计划偶尔写一两篇文章，但最有可能的是，它们将与这个标签分开发表。

我想对那些激励我并为我的剧集提供反馈的人们表达我诚挚的谢意。这意味着很多，让我的心里充满了幸福。

回头见！:)