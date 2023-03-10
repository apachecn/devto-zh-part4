# CircleCI 工作流重新运行片状 RSpec 示例

> 原文：<https://dev.to/hanachin/circleci-workflows-to-rerun-flaky-rspec-examples-3m8o>

这里是概述。重点是将运行所有测试和运行失败测试的作业分开。这仅允许您重新运行失败的测试。它可以节省时间，因为您不需要重新运行整个测试套件，您可以只重新运行失败的测试套件。

```
# spec/spec_helper.rb
RSpec.configure do |config|
  config.example_status_persistence_file_path = "spec/examples.txt"
end 
```

```
# .circleci/config.yml
version: 2.1

executors:
  ruby:
    docker:
      - image: circleci/ruby

jobs:
  test:
    executor: ruby
    steps:
      - checkout
      - run: gem install rspec
      - run: rspec --failure-exit-code=0
      - persist_to_workspace:
          root: .
          paths:
            - '*'

  test_again:
    executor: ruby
    steps:
      - attach_workspace:
          at: ~/project
      - run: gem install rspec
      - run: rspec --only-failures

workflows:
  test:
    jobs:
      - test
      - test_again:
          requires: [test] 
```

作业运行整个测试套件，这些套件有时会因为不可靠的测试而失败。但是没关系。我将在`test_again`作业中重新运行失败的测试。

RSpec 有 [`example_status_persistence_file_path`](https://relishapp.com/rspec/rspec-core/docs/command-line/only-failures) 配置来存储测试结果。
在这个`.circleci/config.yml`中，我使用 [`persist_to_workspace`](https://circleci.com/docs/2.0/configuration-reference/#persist_to_workspace) 保存`test`作业的测试结果，并将它们传递给`test_again`作业。

`test_again`作业`requires`的`test`作业，意味着`rspec`无论测试通过与否都成功退出运行`test_again`作业。

> 作业必须成功启动的作业列表
> [https://circle ci . com/docs/2.0/configuration-reference/#需要](https://circleci.com/docs/2.0/configuration-reference/#requires)

为此，将`--failure-exit-code=0`选项传递给`rspec`命令。

c.f. [`\-\-failure\-exit\-code\`选项(退出状态)——命令行——RSpec 核心——RSpec——津津乐道](https://relishapp.com/rspec/rspec-core/v/3-8/docs/command-line/failure-exit-code-option-exit-status)

然后`test_again`作业通过 [`attach_workspace`](https://circleci.com/docs/2.0/configuration-reference/#attach_workspace) 接收`test`作业的测试结果，并运行带有`--only-failures`选项的`rspec`，仅运行失败的测试..

c.f. [Only Failures -命令行- RSpec Core - RSpec - Relish](https://relishapp.com/rspec/rspec-core/docs/command-line/only-failures)

## 样本项目

我创建了一个示例存储库

[https://github . com/hanchin/rsec-soumen](https://github.com/hanachin/rspec-soumen)

它有随机失败的测试。

```
using Module.new {
  refine(Integer) do
    def 冷やしそうめんがうまい?
      self >= 25
    end
  end
}

RSpec.describe '冷やしそうめん' do
  it { is_expected.to be }

  context '気候変動' do
    let(:temperature) { rand(10..40) }

    subject { temperature.冷やしそうめんがうまい? }

    it { is_expected.to be }
  end
end 
```

然后在`test`作业和`test_again`作业中测试失败。

[![](img/2a58fdca3836baf4e1660053faa08956.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_IuH132--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vu3zjm49bb47nv32ev7d.png)
[https://circle ci . com/workflow-run/2 cced 3 B3-59 C4-4e3e-9f 53-33172388 b74b](https://circleci.com/workflow-run/2cced3b3-59c4-4e3e-9f53-33172388b74b)

您可以看到`test`作业运行了整个测试套件，它失败了，但被标记为成功。

[![](img/4455dd979fd59da0f940b42cd356079b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4pndpE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u06lpfqil6ujyopcwfxu.png)
[【https://circci . com/GH/hanchin/rsec-soumen/6】](https://circleci.com/gh/hanachin/rspec-soumen/6)

并且`test_again`作业仅运行失败的测试。

[![](img/447c63eab6a77282c7763710da0a812a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---M25aWeu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x53529a2dmbb4db8f793.png)
[【https://circci . com/GH/hanchin/rsec-soumen/7】](https://circleci.com/gh/hanachin/rspec-soumen/7)

您可以从失败的作业重新运行工作流。
作业通过的时间。

[![](img/bf9be152c39d38b3b2e11d7257ab914b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vt5Gb1Bb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmhd13irngjfvqx3ijum.png)
[![](img/96e800e158f02c16f51f2cbc7f9a08d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mYIYA8ec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08a7j90z8qst51mb43nb.png)
[https://circle ci . com/workflow-run/09fb 370d-bbdd-4648-acba-0 Bebe 5a 7 bddf](https://circleci.com/workflow-run/09fb370d-bbdd-4648-acba-0bebe5a7bddf)

## 结论

我将测试作业分开来运行所有测试和运行失败的测试。

结果:

*   如果测试在`test_again`中再次失败，我不需要重新运行整个测试套件，我可以重新运行失败的测试。

*   我可以重新运行失败的测试，而不需要对测试代码做任何修改，比如`rspec-retry`或者扩展`Capybara.default_wait_time`之类的。

*   我不需要调查不可靠的测试，只需重新运行失败的测试。
    我知道修复易变的测试是好的，但它可能需要很多次，
    我避免这样做。

*   我失败的测试在`test_again`任务中自动重新运行一次。
    我也可以连锁`test_again_again`、`test_again_again_again`的工作😘

*   也许我可以在工作中统计不可靠的测试。

干杯🍻