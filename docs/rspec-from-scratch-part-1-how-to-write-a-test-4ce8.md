# TDD 基础:如何编写一个 RSpec 测试

> 原文：<https://dev.to/jeremy/rspec-from-scratch-part-1-how-to-write-a-test-4ce8>

测试可能是令人生畏的，尤其是对初学者来说，但它真的不必如此。稍加练习，编写测试实际上很有趣。因此，让我们从头开始学习 RSpec，并尝试一下这个测试。我们将从头开始，学习如何使用测试驱动开发(TDD)来编写测试，同时解决基本的 Codewars 挑战。当我们这样做的时候，请记住我所使用的文件结构对于解决一个简单的代码挑战来说是多余的。然而，对于一个更大的项目来说，这是你应该做的，所以我故意这样教你，以便你理解如何正确地安装、设置和使用 RSpec。

**安装**

从您的项目目录中，运行`gem install rspec`。

[![gem install rspec](img/91a721887db746f6102b843b1255734a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--The-ZeH_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wloyemubgrb0ch3qhsvg.png)

现在运行`rspec --init`

[![rspec --init](img/ee02241bbdd086a5f85be4d6f369dbdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wRrHQo1w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w5n0eao7079kn3kbeawn.png)

**设置**

您将看到这个命令在您的项目目录中创建了一个`.rspec`文件，以及一个包含`spec_helper.rb`文件的`spec`文件夹。这些文件是用于配置的，你现在不需要担心它们，除了说我们将需要我们的`spec_helper.rb`文件在我们写测试的文件中。稍后会有更多的介绍。

我们今天要面对的 Codewars 挑战叫做[降序](https://www.codewars.com/kata/5467e4d82edf8bbf40000155)。简要情况如下:

> 您的任务是创建一个函数，它可以将任何非负整数作为参数，并以降序返回它的数字。本质上，重新排列数字以创建尽可能高的数字。
> 
> 示例:
> 输入:21445 输出:54421
> 
> 输入:145263 输出:654321
> 
> 输入:1254859723 输出:9875543221

让我们从创建一个`descending_order.rb`文件开始，我们将在这个文件中编写我们的解决方案。

[![descending_order.rb](img/1d1fe0e463390a6b2017fcfa9b44d969.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q7iFj1Or--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/leu5tfvb6u65xqbh1f2w.png)

现在，在您的`spec`目录中，创建一个`descending_order_spec.rb`文件。这是我们写测试的地方。

[![descending_order_spec.rb](img/6d42d6992a5c6152a4d0083ed0eb6e56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zghyRcYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r0btz7suj9v7jw551141.png)

每当您创建一个文件来编写测试时，它必须位于`spec`文件夹中，并且它必须遵循这个惯例:`[filename]_spec.rb`。当你运行 RSpec 时，它会在你的`spec`文件夹中查找任何`_spec.rb`文件，并运行这些文件。如果您的文件名不符合该约定，RSpec 将无法找到它们。

顶部的`spec/descending_order_spec.rb`需要你的`spec_helper`文件和你的`descending_order.rb`文件。

```
require_relative './spec_helper.rb'
require_relative '../descending_order.rb' 
```

Enter fullscreen mode Exit fullscreen mode

**编写测试**

测试驱动开发包括编写一个失败的测试，然后编写代码让它通过，接着是重构。因为失败的测试输出在终端中是红色的，而通过的测试输出是绿色的，所以 TDD 过程经常被描述为红绿重构。

首先，我们从指定我们正在测试的内容的`describe`块开始。在这种情况下，我们将测试一个方法，当我们编写它的时候，我们将称之为`descending_order`。

```
describe 'descending_order' do

end 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们写一个`it`块，描述我们的方法工作时会做什么。在这一点上，我们知道我们的方法需要做什么，但是我们不知道如何具体实现它。

```
describe 'descending_order' do
  it 'takes any integer and returns its digits in descending order' do

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们调用我们尚未编写的方法，传入一个示例参数，RSpec 将使用它来测试我们的`descending_order`方法中的代码。为了方便和组织，将其设置为一个变量。这将使以后测试其他值更加容易。

```
describe 'descending_order' do
  it 'takes in any integer and returns its digits in descending order' do
    ordered_1 = descending_order(1587956342)

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们写下我们的期望。让我们思考一下这一部分。这是我们目前所知道的:

1.  我们需要编写一个名为`descending_order`的方法，它接受一个整数值，并从最高到最低对数字进行排序。
2.  我们的测试将调用`descending_order`，将`1587956342`作为参数传入。
3.  我们的方法需要返回`9876554321`。
4.  因此，由于我们的方法调用被设置为变量`ordered_1`，如果我们的代码有效，我们期望`ordered_1`等于`9876554321`。

RSpec 的美妙之处在于它为我们提供了几种方法，我们可以用它们来设置对工作代码的期望。在这个例子中，我们使用了方法`expect`、`to`和`equal`。

```
describe 'descending_order' do
  it 'takes in any integer and returns its digits in descending order' do
    ordered_1 = descending_order(1587956342)

    expect(ordered_1).to eq(9876554321)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚编写了我们的第一个测试！

**运行测试套件**

通过在终端中键入`rspec`来运行测试。或者，如果您有一个非常大的测试套件，这很有用，您可以运行特定的测试文件，比如:`rspec spec/descending_order_spec.rb`。

**专业提示:**当您开始使用更大的测试套件时，运行您的测试并看到几十个失败的例子会变得令人不知所措。追加`--fail-fast`或简单的`--f-f`将一次显示一个失败的测试。像这样:`rspec --f-f`。

运行您的测试应该会得到类似如下的输出:

[![failing test output](img/aaf51d5a0de0cf81fede19df38caa201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VWa_3qZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rfvqjj019j0paeu8oba.png)

这是我们的第一个错误。让我们一行一行地分解它。

`1) descending_order takes in any integer and returns its digits in descending order`

第一行是我们测试中的`it`块。它告诉我们这个方法应该做什么，但是没有。

`Failure/Error: ordered_1 = descending_order(1587956342)`

下一行告诉我们测试中失败发生的位置，在本例中，当我们将`descending_order`方法赋给`ordered_1`变量时失败。

`NoMethodError:
undefined method 'descending_order' for ...`

这一行是实际发生的错误。这里 RSpec 告诉我们，它在我们测试的文件中找不到一个`descending_order`方法，这是有意义的，因为我们还没有编写一个方法。

**提示:**现在要养成阅读整个错误信息的习惯，而不仅仅是特定的异常。整个错误包含有价值的信息，有助于您调试代码。我怎么强调都不为过。*阅读整个错误信息*。

还有，这条线在这里，就在`NoMethodError`
的下面

```
# ./spec/descending_order_spec.rb:6:in `block (2 levels) in <top (required)>' 
```

Enter fullscreen mode Exit fullscreen mode

起初看起来可能不太有用，但是这是非常有用的信息，因为它告诉您在测试套件中失败的确切行。在这种情况下，失败发生在`spec`目录中文件`descending_order_spec.rb`的第 6 行，如该行的前半部分所示:

`# ./spec/descending_order_spec.rb:6`

如果你发现自己在一个不熟悉的应用程序上工作，并且有一个完整的测试套件，进入规格并阅读测试总是一个好主意。它们会给你关于代码如何工作/应该如何工作的有价值的信息，当它们运行时，你会更好地理解测试。

例如，在我们的测试中，我们有一行`expect(ordered_1).to eq(9876554321)`。当您阅读整个测试并看到`ordered_1`包含参数`1587956342`时，这一行就更容易理解了。所以现在我们马上明白，当传递了`1587956342`时，该方法应该返回`9876554321`。从测试输出本身来看，这并不明显。

这可能是一个很好的时机来提及它是极其重要的，你的测试是干净的，有组织的，并且可以被任何在你之后阅读它们的人理解。测试的目的之一是防止你的代码回归。当您的代码将来需要调试时，无论是您还是其他人，测试套件都应该能够立即被阅读和理解。

所以现在我们需要定义一个`descending_order`方法。就这么办吧。打开`descending_order.rb`并写下以下内容:

```
def descending_order(num)

end 
```

Enter fullscreen mode Exit fullscreen mode

再次进行测试。

[![new error](img/b60e9c6de4f48b656fd93e8d0d12d1d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zoU4bfpV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9fh83r5sbfxvjunnzot.png)

我们现在有一个新的错误，这是一个好消息！这意味着我们正在取得进展。在我们测试的这一点上，RSpec 调用了`descending_order`方法，并传递了它`1587956342`。它期望该方法随后返回`9876554321`，但是它却返回了`nil`。这是因为我们只定义了方法，还没有赋予它任何排序能力。

我们知道 Ruby 有一个方便的`#sort`方法，所以让我们试试。

```
def descending_order(num)
  num.sort
end 
```

Enter fullscreen mode Exit fullscreen mode

[![another error](img/ee65202ee959a5294f92d7f80029c044.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3OGlwfo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncu1pkqojfigz2fovd41.png)

又一个错误！这是反直觉的，因为遇到一个又一个错误可能会令人沮丧，但请始终记住，新的错误意味着您正在朝着解决方案前进(至少大部分时间是这样)。

测试输出告诉我们，我们试图对一个整数调用`#sort`，这是不允许的。`1587956342`是单个整数值，`#sort`不能像那样对单个数字进行排序。我们怎样才能得到这些数字呢？我们需要把整数拆分成一个数组。我们可以通过多种方式做到这一点，但最简单的方式是使用`#digits`方法。

```
def descending_order(num)
  num.digits.sort
end 
```

Enter fullscreen mode Exit fullscreen mode

再次运行我们的测试会得到以下输出:

[![returns a sorted array](img/98b709d10df1bee419c88d3448849f42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GdarEejP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ovfwn0caf9h6zp2fxdv5.png)

我们越来越近了！在这种情况下，RSpec 期望的是`9876554321`，但得到的是`[1, 2, 3, 4, 5, 5, 6, 7, 8, 9]`，一个排序后的数组！问题(其中之一)是它是按升序排序的，我们需要它按降序排序。

我们可以这样做，使用我最喜欢的操作员之一，`<=>`，[宇宙飞船](https://stackoverflow.com/questions/827649/what-is-the-ruby-spaceship-operator) :

```
def descending_order(num)
  num.digits.sort{ |a, b| b <=> a }
end 
```

Enter fullscreen mode Exit fullscreen mode

[![array sorted in descending order](img/41add9ac20d51d8e3fa216b8d825ccf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OMEsVMS4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6vf0vkmzfjioyx1vwoh.png)

太棒了。我们的数组按正确的顺序排序，但现在我们需要它再次成为一个整数。让我们加入它。

```
def descending_order(num)
  num.digits.sort{ |a, b| b <=> a }.join
end 
```

Enter fullscreen mode Exit fullscreen mode

[![string](img/50345f5b666ed1444c9d934871fc6fd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JYR_Sx_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/psyooixrq9wpr1oydjik.png)

我们已经很接近了！让我们把这个字符串转换成一个整数。

```
def descending_order(num)
  num.digits.sort{ |a, b| b <=> a }.join.to_i
end 
```

Enter fullscreen mode Exit fullscreen mode

[![passing test](img/63f7b89aab63d526e93743d874f06ab1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5JvE0kC9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mnkqqzkty41gr1logh83.png)

有用！我们成功地使用 TDD 解决了这个代码挑战。不过，我们还没有完成。

**重构测试和代码**

重要的是要确保你的测试既简单又彻底。让我们再添加几个变量，以确保一切正常。因为我们正在解决一个 Codewars 挑战，我们将使用他们使用的。

```
describe 'descending_order' do
  it 'takes in any integer and returns its digits in descending order' do
    ordered_1 = descending_order(1587956342)
    ordered_2 = descending_order(21445)
    ordered_3 = descending_order(145263)
    ordered_4 = descending_order(1254859723)

    expect(ordered_1).to eq(9876554321)
    expect(ordered_2).to eq(54421)
    expect(ordered_3).to eq(654321)
    expect(ordered_4).to eq(9875543221)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当您现在运行`rspec`时，测试应该仍然通过。

考虑边缘情况也很重要。例如，如果我们的方法传递一个一位数的整数，它会做什么？我们可以猜测，但让我们来确定一下。因为我们正在测试这个方法的不同功能，所以我们将为它编写一个新的测试。

我们将从我们的`it`块开始:

```
it 'returns the original integer if it only has one digit' do

end 
```

Enter fullscreen mode Exit fullscreen mode

传入参数并设置为变量:

```
it 'returns the original integer if it only has one digit' do
  int_0 = descending_order(0)
  int_1 = descending_order(1)

end 
```

Enter fullscreen mode Exit fullscreen mode

并设定我们的期望:

```
it 'returns the original integer if it only has one digit' do
  int_0 = descending_order(0)
  int_1 = descending_order(1)

  expect(int_0).to eq(0)
  expect(int_1).to eq(1)
end 
```

Enter fullscreen mode Exit fullscreen mode

因此，现在我们针对这一挑战的完整测试套件如下所示:

```
require_relative './spec_helper.rb'
require_relative '../descending_order.rb'

describe 'descending_order' do
  it 'takes in any integer and returns its digits in descending order' do
    ordered_1 = descending_order(1587956342)
    ordered_2 = descending_order(21445)
    ordered_3 = descending_order(145263)
    ordered_4 = descending_order(1254859723)

    expect(ordered_1).to eq(9876554321)
    expect(ordered_2).to eq(54421)
    expect(ordered_3).to eq(654321)
    expect(ordered_4).to eq(9875543221)
  end

  it 'returns the original integer if it only has one digit' do
    int_0 = descending_order(0)
    int_1 = descending_order(1)

    expect(int_0).to eq(0)
    expect(int_1).to eq(1)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

运行`rspec`显示我们的两个测试仍然通过。恭喜你！您刚刚使用 TDD 和 RSpec 解决了一个 Codewars 挑战。在这一点上，如果我们有超过三行的方法，我们将重构代码，与最佳实践和红绿重构方法保持一致。现在没必要这样，但这是你不想忘记的重要的最后一步。当我们前进时，请记住这些基本原则，通过实践，测试驱动的开发将成为第二天性。