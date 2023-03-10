# 我的一系列不幸的错误(在写测试时)

> 原文：<https://dev.to/briwa/a-series-of-my-unfortunate-mistakes-when-writing-tests-h8m>

很久以前，当我开始写测试的时候(实际上，不是很久，可能是几年前)，我是一个天真的年轻人。我讨厌 bug 所以我写测试，而且是根据我当时有限的知识写的。

天真和不了解最新的参考资料是有代价的。从每一个被拒绝的公关审查或回归错误中，我从我的错误中学到了很多，这让我意识到我还有很多需要改进的地方。对我来说，不得不通过尝试和错误来学习确实是不幸的，但对你来说不一定是不幸的！

我说，开发人员们，如果你们觉得你们的测试不够好，或者你们的 PRs 由于测试质量的缺乏而被 QA 团队拒绝了太多次，也许你们会发现这篇文章很有用。我将与你分享我在编写测试时犯的五大错误，以及为什么你应该避免它们。

* * *

在此之前，声明:下面的示例代码是使用 Jest 作为测试框架用 Javascript 编写的。我的重点只是 Javascript，所以我不能对其他的做太多评论，不确定它是否可以应用。此外，这些只是简化的示例，并不代表实际的使用案例。只是为了让大家明白这一点。

好吧。直接看这个例子。按说我在写这个类:

```
class Account {
  constructor (initialBalance = 0) {
    this.balance = initialBalance
  }

  deposit (money) {
    this.balance += money
  }

  withdraw (money) {
    this.balance -= money
  }
} 
```

现在，这个类很简单。它有办法存入和取出一笔钱来改变收支平衡。我编写测试的旅程从这里开始。

## 1。测试不简单

我想测试的第一件事是`.deposit`方法。在我看来，测试必须非常具体，其他阅读测试的人甚至不需要看到实际的代码。

```
const account = new Account()

describe('Account class', () => {
  describe('.deposit', () => {
    test('Should increment the account balance by the amount', () => {
      const increment = 200
      const originalBalance = account.balance
      account.deposit(increment)
      expect(account.balance).toBe(originalBalance + increment)
    })
  })
}) 
```

测试看起来不错，对吧？它有原始余额，它有要增加的金额，它断言原始余额加上增量。事实上，如果我想改变增量的数量，我只需要改变`increment`变量，测试仍然会通过。就是这样。超级简单。

然后出现了一个新的要求。作为奖励，每存入一笔金额，都会在金额的基础上增加 2%(不要问我为什么，这是 PM...).

```
 deposit (money) {
    this.balance += (money * 1.02)
  } 
```

嗯，是的，好吧。所以测试应该是....

```
 test('Should increment the account balance by the amount plus 2% incentive', () => {
      const increment = 200
      const originalBalance = account.balance
      // PLEASE SEE TEH CODE FOR THE CLASS FOR REFERENCE
      const incrementPlusIncentive = increment * 1.02
      account.deposit(increment)
      expect(account.balance).toBe(originalBalance + incrementPlusIncentive)
    }) 
```

天啊，这是什么怪物？我的想法是把它弄清楚，但我最终把它弄得更复杂了。此外，我将代码中的逻辑复制到测试中。这是不对的。

实际上，测试代码应该只明确地陈述你正在测试的内容(输入->输出)。那里不应该有逻辑代码；它属于你正在测试的代码。这就是为什么，一个改进的版本应该是:

```
 test('Should increment the account balance by the amount plus 2% incentive', () => {
      account.deposit(100)
      expect(account.balance).toBe(102)
    }) 
```

给你。保持简单。我存了 100 英镑，现在我的余额是 102 英镑。符合要求吗？绝对的！这才是最重要的。

## 2。每次测试都没有保持干净的状态

我的下一个任务是编写测试的剩余部分。的确如此。

```
const account = new Account()

describe('Account class', () => {
  describe('.deposit', () => {
    test('Should increment the account balance by the amount plus 2% incentive', () => {
      account.deposit(100)
      expect(account.balance).toBe(102)
    })
  })

  describe('.withdraw', () => {
    test('Should decrement the account balance by the amount', () => {
      account.withdraw(100)
      expect(account.balance).toBe(2)
    })
  })
}) 
```

嗯，是的，看起来不错。然而，你们中的一些人可能已经注意到了:有一股代码的味道。为什么测试共享一个`account`实例？这难道不会让测试的顺序变得重要吗？如果我们调换顺序，它肯定会坏掉。这是不对的。

```
describe('Account class', () => {
  describe('.deposit', () => {
    test('Should increment the account balance by the amount plus 2% incentive', () => {
      const account = new Account()
      account.deposit(100)
      expect(account.balance).toBe(102)
    })
  })

  describe('.withdraw', () => {
    test('Should decrement the account balance by the amount', () => {
      const account = new Account()
      account.withdraw(100)
      expect(account.balance).toBe(-100)
    })
  })
}) 
```

通过在每个测试中创建`account`实例，可以确保测试从一个干净的石板开始。它可以随意修改，因为它包含在特定测试的范围内，并且是相互独立的。这样，测试的顺序就无关紧要了。比方说，如果我们使用一个并行运行的测试运行程序，并随机安排测试的顺序，它仍然会顺利通过。

顺便说一下，还有 [`beforeEach/afterEach`(或`setup/teardown` )](https://jestjs.io/docs/en/setup-teardown.html) 助手，我们也可以用它来初始化和清理每个测试套件，但在这里解释起来相当复杂，所以可能会在另一篇文章中介绍。

## 3。没有正确地声明状态

接下来，这个项目变大了，显然有一些内务处理在进行，现在所有的代码都要被注释，放入一个合适的文件等等。

```
/**
 * Account class.
 */
class Account {
  /**
   * Constructor function.
   * 
   * This sets the initial balance when initializing the instance.
   * 
   * @param {Number} initialBalance 
   */
  constructor (initialBalance = 0) {
    this.balance = initialBalance
  }

  /**
   * Increment the balance by the given sum of the amount.
   * An incentive of 2% of the amount will be added
   * for each deposited amount.
   * 
   * @param {Number} money 
   */
  public deposit (money) {
    this.balance = (money * 1.02)
  }

  /**
   * Decrement the balance by the given amount.
   * 
   * @param {Number} money 
   */
  public withdraw (money) {
    this.balance -= money
  }
} 
```

好了，完成了。我没有注意到任何问题(或者我有吗？😏你很快就会知道了)。我检查了 Jest 控制台，它说...

```
Account class
  .deposit
    ✓ Should increment the account balance by the amount plus 2% incentive (5ms)
  .withdraw
    ✓ Should decrement the account balance by the amount 
```

很明显，还没通过。咄。提交、PR 审查、CI 构建通过、合并和部署。那是一个有趣的星期一。

...但也不尽然。用户尖叫着说他们的余额被重置为他们存入的金额。发生了什么事？当测试通过时，这是怎么发生的？

我看了看我的代码，看了看测试，似乎没什么问题。是初始余额吗？因为我没有为此进行测试(哎呀)。所以我继续更新测试如下:

```
 describe('.deposit', () => {
    test('Should increment the account balance by the amount plus 2% incentive', () => {
      const account = new Account(100)

      account.deposit(100)
      expect(account.balance).toBe(202)
    })
  }) 
```

瞧，不仅仅是用户，Jest 现在也在尖叫(？)

```
 ● Account class › .deposit › Should increment the account balance 
    by the amount plus 2% incentive

    expect(received).toBe(expected) // Object.is equality

    Expected: 202
    Received: 102

      11 |
      12 |       account.deposit(100)
    > 13 |       expect(account.balance).toBe(202)
         |                               ^
      14 |     })
      15 |   })
      16 |

      at Object.toBe (__tests__/index.test.js:13:31) 
```

bug 出现了！这正是用户报告的内容。现在测试实际上失败了。在查看代码后(您可以与本节开头的代码进行比较)，我注意到了一个小错误:

```
 deposit (money) {
    // The plus was missing 🤮
    this.balance += (money * 1.02)
  } 
```

是的，给你。一个被认为无害的重构导致了一个 bug，可能加号被意外删除了。而且测试也抓不住。我应该一开始就用正确的方式写出来。

如果代码是关于值的累加(而不是值的赋值)，那么它*有*要以这样的方式进行测试，即先前的值与给定的值累加。前面的断言有些不完整，因为它只是在测试赋值。

```
 // 🤔 
  describe('.deposit ❌', () => {
    test('Should increment the account balance by the amount plus 2% incentive', () => {
      const account = new Account() //... What's the previous value?

      account.deposit(100) // Amount is 100
      expect(account.balance).toBe(102) // Final value is 102...?
    })
  })

  // 😎
  describe('.deposit ✅', () => {
    test('Should increment the account balance by the amount plus 2% incentive', () => {
      const account = new Account(100) // Previous value is 100

      account.deposit(100) // Amount is 100
      expect(account.balance).toBe(202) // Final value is 202
    })
  }) 
```

为了打好这个结，还必须测试构造函数。这确保了实例化部分被正确地覆盖(也许如果构造函数有一些逻辑，它也可以被断言)。

```
 describe('constructor', () => {
    test('Should set the initial balance when instantiated', () => {
      const account = new Account(100)
      expect(account.balance).toBe(100)
    })
  }) 
```

也许这一节相当具体，但重点是，总是测试状态的整个流程(之前/之后，I/O)，而不仅仅是部分。至少这是我学到的。

## 4。没有恰当地组织测试

我从 QA 团队那里得知，我没有正确地捕捉到边缘案例。`.deposit`中的值可以是任何值，并且错误不够直观。

此外，新的要求来了:该帐户应该能够存款超过一个单一的数额，然后作出了一笔。

好吧。`.deposit`代码现在看起来像这样:

```
 /**
   * Increment the balance by the given sum of the amount.
   * An incentive of 2% of the amount will be added
   * for each deposited amount.
   * Only number is allowed, otherwise an error is thrown.
   * Also, the number should be greater than 0.
   * 
   * @param {Number[]} ...args 
   */
  deposit (...args) {
    if (args.length === 0) {
      throw new Error('Please provide at least one argument.')
    }

    const amount = args.reduce((total, value) => {
      const number = parseInt(value, 10)
      if (isNaN(number)) {
        throw new Error('Please specify a number as the argument.')
      }

      if (number <= 0) {
        throw new Error('Please specify a number greater than 0.')
      }

      return total + (number * 1.02)
    })

    this.balance += amount
  } 
```

...但是测试看起来并不那么好

```
 describe('.deposit', () => {
    test('Should throw an error when no amount is given', () => {
      const account = new Account()
      expect(() => account.deposit()).toThrowError('Please provide at least one argument.')
    })

    test('Should throw an error when amount given is not a number', () => {
      const account = new Account()
      expect(() => account.deposit('a', 'b', 'c')).toThrowError('Please specify a number as the argument.')
    })

    test('Should increment the account balance by the sum of the amount plus 2% incentive, only when the amount is greater than 0 otherwise it should throw', () => {
      const account = new Account(100)

      account.deposit(100, 200)
      expect(account.balance).toBe(406)

      // ...but not less than 0!
      expect(() => account.deposit(0, 400, -200)).toThrowError('Please specify a number greater than 0.')
    })
  }) 
```

哇，考试的最后一部分相当难。无论什么🙄。任务完成了，测试通过了。

```
 .deposit
    ✓ Should throw an error when no amount is given (4ms)
    ✓ Should throw an error when amount given is not a number (1ms)
    ✓ Should increment the account balance by the sum of the amount plus 2% incentive, only when the amount is greater than 0 otherwise it should throw (5ms) 
```

然而，QA 团队说测试一团糟！很难理解，最后一部分测试做的太多了。一般来说，最好将测试划分到多个上下文中，这样就有多层条件要断言，并且一个测试应该简单地基于上下文做一件事情。

一个改进的版本将是:

```
 describe('.deposit', () => {
    describe('When no argument is provided', () => {
      test('Should throw an error', () => {
        const account = new Account()
        expect(() => account.deposit()).toThrowError('Please provide at least one argument.')
      })
    })

    describe('When the arguments are provided', () => {
      describe('And the arguments are invalid', () => {
        test('Should throw an error', () => {
          const account = new Account()
          expect(() => account.deposit('a', 'b', 'c')).toThrowError('Please specify a number as the argument.')
        })
      })

      describe('And the arguments are valid', () => {
        describe('And the arguments are less than zero', () => {
          test('Should throw an error', () => {
            const account = new Account()
            expect(() => account.deposit(0, 400, -200)).toThrowError('Please specify a number greater than 0.')
          })
        })

        describe('And the arguments are all more than zero', () => {
          test('Should increment the account balance by the sum of the amount plus 2% incentive', () => {
            const account = new Account(100)
            expect(account.balance).toBe(100)

            account.deposit(100, 200)
            expect(account.balance).toBe(406)
          })
        })
      })
    })
  }) 
```

当代码变得更加复杂时，多层上下文非常有用。当它已经像那样被分割成层时，添加更多的上下文就更容易了。例如，如果我要添加一个新的验证(也许应该有一个存放的最大数量)，并且我应该为此添加一个测试，我知道应该把它们放在结构中的什么地方，整洁美观。

层的顺序主要是我的偏好。我喜欢在顶部看到边缘情况，在底部看到实际的逻辑，有点像代码中如何编写保护(或实际的验证)。

下面是 Jest 输出的样子:

```
 .deposit
    When no argument is provided
      ✓ Should throw an error (7ms)
    When the arguments are provided
      And the arguments are invalid
        ✓ Should throw an error (2ms)
      And the arguments are valid
        And the arguments are less than zero
          ✓ Should throw an error (2ms)
        And the arguments are all more than zero
          ✓ Should increment the account balance by the sum of the amount plus 2% incentive (2ms) 
```

现在我不得不同意 QA 团队的观点。

## 5。不信任您正在使用的库

利益相关者说，有黑客从账户中提取不属于他们的钱。由于这个问题，`.withdraw`函数不会简单地扣除余额；它必须通过一些验证脚本魔术，以便它知道它是否被黑客篡改(我不知道如何，这只是一个示例代码)。

```
 /**
   * Decrement the balance by the given amount.
   * It is now using a validator from backend
   * which I don't know how it works.
   * 
   * @param {Number} money 
   */
  withdraw (money) {
    const currentBalance = this.validateAndWithdraw(money)
    this.balance = currentBalance
  }

  validateAndWithdraw (money) {
    // This validator might throw an error if the transaction is invalid!!!
    return superDuperValidatorFromBackend(money)
  } 
```

由于在 Jest 中实际运行它的昂贵成本，我宁愿嘲笑做验证的函数。只要它不会给我一个错误，并给我实际的平衡，它应该是好的。

```
 describe('.withdraw', () => {
    describe('Given a valid withdrawal', () => {
      test('Should set the balance after withdrawal', () => {
        const account = new Account(300)

        // Override this function to avoid having to actually request from backend.
        // It should just return the balance without any error thrown.
        jest.spyOn(account, 'validateAndWithdraw').mockImplementationOnce(() => 200)

        expect(() => account.withdraw(100)).not.toThrow()
        expect(account.validateAndWithdraw).toHaveBeenCalledWith(100)
        expect(account.balance).toBe(200)
      })
    })
  }) 
```

我在那里添加了`not.toThrow()`,这样我就知道当我调用`.withdraw`函数时，不会抛出错误，因为我嘲笑了它。对吗？对吗？

> 不对。-质量保证团队

最终，我了解到我写的测试应该只覆盖我的代码的业务逻辑。测试它是否被抛出不应该是我测试的责任，因为函数实现已经被 Jest 嘲讽了，正如我在测试中指定的那样，这样错误就不会被抛出。不需要断言它该不该扔，因为它绝对不会扔！

> ...但是你怎么能这么确定呢？-我笨拙的测试技巧

你可以随时检查 Jest 的知识库、源代码以及他们是如何测试它们的，以及它是否通过了测试。甚至可能有确切的代码，谁知道呢。关键是，我必须信任我正在使用的库，确保他们的代码工作是他们的测试责任，而不是我的。我的测试应该集中在我代码的实际逻辑上。

```
 describe('.withdraw', () => {
    describe('Given a valid withdrawal', () => {
      test('Should set the balance after withdrawal', () => {
        const account = new Account(300)

        // Override this function to avoid having to actually request from backend.
        // It should just return the balance without any error thrown.
        jest.spyOn(account, 'validateAndWithdraw').mockImplementationOnce(() => 200)

        account.withdraw(100)
        expect(account.validateAndWithdraw).toHaveBeenCalledWith(100)
        expect(account.balance).toBe(200)
      })
    })
  }) 
```

就是这样。只允许业务逻辑。

* * *

现在，我的旅程到此结束。谁知道未来会发生什么(错误)...

此外，其中一些错误可能很明显。但是这些观点仍然有效。我只是想分享一下。如果你对这些建议有任何反馈，或者也许这根本不是一个错误，让我们在下面的评论区讨论吧。

我希望你喜欢阅读这篇文章，谢谢你！

* * *

封面图片由[杰米街](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/mistakes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄。