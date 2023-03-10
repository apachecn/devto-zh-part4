# 像奇迹一样为你的花园生长

> 原文：<https://dev.to/everythingfunct/like-miracle-grow-for-your-garden-541b>

在我写的关于我开发的[蔬菜](https://gitlab.com/everythingfunctional/vegetables)测试框架的[上一篇文章](https://everythingfunctional.wordpress.com/2019/02/10/the-power-of-a-testing-framework/)中，我谈到了它取得了多大的成功。现在我把它调到了 11。现在，我已经在 Fortran 测试框架中实现了有效的快速检查级别功能。

[![Head Explode](img/3c4d40c191acba0063d6d9f76aed45f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bFNEFeI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://everythingfunctional.files.wordpress.com/2018/11/giphy-downsized-large.gif%3Fw%3D1100)

对于任何不熟悉 QuickCheck 的人来说，它实际上是一个生成随机值的库，用作测试的示例。但是真正有用的部分是，如果它发现一个导致测试失败的例子，那么它会试图找到导致测试失败的最简单的例子，并向您报告。

我举的最简单的例子就是一个整数应该等于它自己。

```
function checkPassForSameInteger(input) result(result_)
    use Vegetables_m, only: Result_t, assertEquals, assertThat, fail

    class(*), intent(in) :: input
   type(Result_t) :: result_

    type(Result_t) :: example_result

    select type (input)
    type is (integer)
        example_result = assertEquals(input, input)
        result_ = assertThat( &
                example_result%passed(),
                example_result%verboseDescription(.false.))
    class default
        result_ = fail("Expected to get an integer")
    end select
end function checkPassForSameInteger 
```

现在只运行这个测试会得到以下结果。

```
$ test_build/vegetable_driver -q -v -f "passes with the same integer"
Running Tests

A total of 1 test cases

All Passed
Test that
    assertEquals with integers
        passes with the same integer
            Passed after 100 examples
A total of 1 test cases containg a total of 1 assertions 
```

然后，如果我把断言改为`assertEquals(input, input+1)`，我得到如下结果。

```
$ test_build/vegetable_driver -q -v -f "passes with the same integer"
Running Tests

A total of 1 test cases

Failed
Test that
    assertEquals with integers
        passes with the same integer
            Fails with the simplest possible example
            Expected to be true
                User Message:
                    [Expected
                             [0]
                     but got
                             [1]]
1 of 1 cases failed
2 of 2 assertions failed 
```

或者，如果我把断言改为`assertEquals(input, input/2)`，我得到如下结果。

```
$ test_build/vegetable_driver -q -v -f "passes with the same integer"
Running Tests

A total of 1 test cases

Failed
Test that
    assertEquals with integers
        passes with the same integer
            Found simplest example causing failure
            Expected to be true
                User Message:
                    [Expected
                             [1]
                     but got
                             [0]]
1 of 1 cases failed
2 of 2 assertions failed 
```

为了使用快速检查功能，您需要创建一个从`Generator_t`类型扩展而来的派生类型。这需要您为它实现两个功能。您需要`generate`，它将您的生成器类型作为参数，并产生一个包装在`Generated_t`类型中的随机值。然后您需要`shrink`，它将您生成的一个值作为一个`class(*)`，并返回一个比给定的值更“简单”的值，用`ShrunkValue_t`或`SimplestValue_t`包装以指示该值不能再简单了。

我实现了几个基本的生成器和一些帮助函数来生成一些随机值，但是对于大多数情况，您可能需要创建自己的生成器。但是如果可以的话，你可以指定你的代码的一些属性，这些属性应该适用于任何随机值，你真的可以对你的代码进行压力测试。

我认为在这一点上，我拥有了我使用过的功能最全的测试框架之一。我认为这比我见过的任何其他测试框架都要少。胜利的函数式编程原则！