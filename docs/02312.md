# 从 Mocha 迁移到 rspec-mocks

> 原文：<https://dev.to/andyw8/migrating-from-mocha-to-rspec-mocks-2khh>

在 [Financeit](https://www.financeit.io) ，我们的主应用程序使用 RSpec 作为测试套件，但直到最近它才使用 [Mocha](https://github.com/freerange/mocha) (不要与同名的 JavaScript 库混淆)进行存根和嘲讽。

我们最近决定改用 rspec-mocks。出现这种情况的一些原因是:

*   我们已经在其他项目中使用了 rspec-mocks。
*   r spec-mock 有更多的参考和培训材料(书籍、截屏、博客帖子等)。
*   加入团队的新开发人员通常已经熟悉 r spec-mock。

我们对 Mocha 本身没有任何问题——它设计得很好，有很好的文档，有一个负责的维护者。主要原因是要有一个简单和一致的堆栈。

## 接近

由于我们的测试套件很大，我们知道增量方法会更好。

我们利用了 rspec-multi-mock gem 来允许 Mocha 和 rspec-mock 同时使用。

然后我们引入了一个策略，新的规范应该使用 rspec-mocks 而不是 Mocha 来编写。我们的代码审查过程有助于提醒开发人员这一点。

接下来，我们开始转换测试套件的过程。大多数 Mocha 都有到 r spec-mock 的直接映射。为了便于参考，我制作了这张“备忘单”:

| 摩卡 | rspec-模拟 |
| --- | --- |
| `stub(a: 1)` | `double(a: 1)` |
| `mock(a: 1)` | `double(a: 1)` |
| `foo.stubs(:a).returns(1)` | `allow(foo).to receive(:a).and_return(1)` |
| `foo.expects(:a).returns(1)` | `expect(foo).to receive(:a).and_return(1)` |
| `Foo.any_instance.stubs(:a).returns(1)` | `allow_any_instance_of(Foo).to receive(:a).and_return(1)` |

进行这些转换最明显的方法是使用正则表达式。虽然这可以处理大约 80%的转换，但是其余的需要手工检查和编辑，或者需要非常复杂的正则表达式。

作为使用自动化工具更改代码的一般规则，修改 AST(抽象语法树)比操作字符串更好。

Ruby 已经为此提供了一个强大的工具: [RuboCop](https://github.com/rubocop-hq/rubocop) ，它利用了[解析器](https://github.com/whitequark/parser) gem，具有自动纠正功能，可以更改代码以遵循偏好的风格。

## RuboCop 定制警察

在 [Financeit](https://www.financeit.io) ，我们有一个“尖峰周”的概念，开发者可以在这里从事他们个人感兴趣的工作。这给了我一个探索这个领域的机会。

RuboCop 可以用[自定义 cops](https://github.com/rubocop-hq/rubocop/blob/master/manual/extensions.md) 来扩展——文档有些有限，所以需要一些时间来掌握，但之后我发现这是一个非常有效的方法。

我分阶段使用这个应用程序，从`spec/controllers`开始，然后是`spec/models`、`spec/services`等等。

测试套件本身有助于验证转换是否正确，因为错误的转换通常会导致语法错误或测试失败。

由于测试中一些奇怪的方法，我遇到了几次转换不成功的情况，所以我抓住机会重新编写了测试。

我们现在已经转换了大约 95%的测试套件。我们希望很快达到 100%，然后我们可以删除 rspec-多模拟和摩卡宝石。

我已经将 RuboCop 自定义警察发布为 [mocha_to_rspec](https://github.com/andyw8/mocha_to_rspec) gem。