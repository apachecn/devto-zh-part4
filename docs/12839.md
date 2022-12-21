# 使用 Minitest 的种子值追踪依赖于顺序的易变测试

> 原文：<https://dev.to/pezza/using-minitest-s-seed-value-to-track-down-order-dependent-flaky-tests-119a>

当你运行你的测试套件时，有没有想过为什么`--seed #####`会被输出？让我们来看看它是如何成为一个有用的调试工具的。

我们将从一个简单的测试套件开始:

```
require "minitest/autorun"
require "redis"

class Task
  class << self
    attr_accessor :total_completed
  end

  attr_accessor :completed

  def complete
    Task.total_completed ||= 0
    Task.total_completed += 1
    self.completed = true
  end
end

class TaskTest < Minitest::Test
  def test_global_tracking
    assert Task.total_completed.nil?
  end

  def test_complete
    task = Task.new
    task.complete
    assert task.complete
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个测试套件有两个测试，一个检查没有完成的任务，另一个测试完成一个任务。让我们运行该套件几次，看看我们的测试如何公正:

```
❯ ruby test/flaky_test.rb
Run options: --seed 3199

# Running:

..

Finished in 0.000709s, 2820.8743 runs/s, 2820.8743 assertions/s.
2 runs, 2 assertions, 0 failures, 0 errors, 0 skips

❯ ruby test/flaky_test.rb
Run options: --seed 40573

# Running:

.F

Failure:
TaskTest#test_global_tracking [test/flaky_test.rb:20]:
Expected false to be truthy.

Finished in 0.000956s, 2092.0506 runs/s, 2092.0506 assertions/s.
2 runs, 2 assertions, 1 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

果然，我们有一个古怪的测试。第一次运行套件时，一切都通过了，但第二次在`assert Task.total_completed.nil?`时失败了。

默认情况下，Minitest 以随机顺序运行所有测试，以帮助防止测试变得依赖于顺序。在上面的例子中，测试失败是因为我们忽略了在测试之间重置共享的全局状态。如果首先运行`test_global_tracking`,套件将会是绿色的，如果不是，我们将会失败。因为这个套件很小，所以 bug 很容易被发现，但是当您的套件变得有很多测试用例时，就很难重现导致失败的场景。

我多年来使用的一种简单的调试方法是将断言改为`if`。然后在失败时调用`pry`或`puts`当前状态，然后在命令行上的无限循环中运行测试套件，直到失败触发。这种方法对于具有大型套件的应用程序来说并不理想，因为它可能会成为一个非常耗时的过程来获得正确的顺序。

`seed`去营救。

您可以使用失败运行的`seed`值作为命令行选项，以相同的顺序重新运行您的测试。

```
❯ ruby test/flaky_test.rb --seed 40573
Run options: --seed 40573

# Running:

.F

Failure:
TaskTest#test_global_tracking [test/flaky_test.rb:20]:
Expected false to be truthy.

Finished in 0.001082s, 1848.4287 runs/s, 1848.4287 assertions/s.
2 runs, 2 assertions, 1 failures, 0 errors, 0 skips

❯ ruby test/flaky_test.rb --seed 40573
Run options: --seed 40573

# Running:

.F

Failure:
TaskTest#test_global_tracking [test/flaky_test.rb:20]:
Expected false to be truthy.

Finished in 0.001134s, 1763.6684 runs/s, 1763.6684 assertions/s.
2 runs, 2 assertions, 1 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

哇哦🎉！现在，我们可以直接投入到可靠的调试中，跳过等待我们的测试套件处于正确的顺序。