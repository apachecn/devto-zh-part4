# 测试工厂机器人序列

> 原文：<https://dev.to/arturmartsinkovskyi/factory-bot-sequence-testin-274i>

有一次，我正在开发一个特性，它是对某个实体的 UUID 的验证，这个实体与正则表达式`/^ENT-\d\d\d\.\d\d\d\.\d\d\d$/`相匹配。这是一个由 RSpec 和 FactoryBot 合作的 Rails 项目。我为该特性编写了代码和测试，一切看起来都很好，直到我将它推送到 GitHub 并运行 CI 测试。与本地环境不同，CI 环境被设置为并行运行(这一点在后面会很重要)。CI 似乎在本地环境下运行良好的大约 300 个测试时就崩溃了。经过几个小时的调查，在我看来，工厂中 UUID 的序列给出了任何超过 999 的元素的错误结果，这肯定是不可接受的。我修复了这个问题，但是为了确保一切正常工作，我需要验证序列的结果是正确的。

# 问题

FactoryBot sequence 是一种枚举器，它将从 1 开始的后续整数值传递给一个块，并将该块的结果返回。如果它的块实现正确，它就是一个纯函数，从整数集 n 到值集的映射。下面你可以看到一个序列的语法示例。

```
FactoryBot.define do
  sequence(:uuid) do |n|
    n.to_s
  end
end

FactoryBot.generate(:uuid) # => "0"
FactoryBot.generate(:uuid) # => "1" 
```

它作为测试的**工具**很棒，但作为被测试代码的却不怎么样。我们可以从外部测试工厂机器人序列，检查`generate`的输出，然后用`FactoryBot.sequence_by_name(:UUID).rewind`将序列倒回原始状态。这意味着我们的序列测试将需要首先运行，以避免在倒回序列并再次创建相同的值后出现唯一性冲突。但是为了真实地面对自己，为了确保我们正确地测试，我们需要确保我们的序列在所有可能的情况下都是有效的。我们如何做到这一点？

# 解

答案非常简单明了——我们应该使用普通的 Ruby 代码来解决这个问题。FactoryBot sequence 是一种向工厂提供唯一值的机制，它是测试领域 DSL 的一部分，并且在其中运行，因此它不可从外部测试的事实是用户友好设计中不可避免的一部分。与其试图智取库接口和测试序列本身，不如考虑这个函数的设计主体。与其试图瞒过库接口和测试序列本身，不如考虑这个函数的主体。实际上，它是一个函数，获取一个整数值，并返回它认为合适的任何反映。这意味着我们可以只在序列中注入这样的函数的代码，然后测试它，而不是把整个 FactoryBot DSL 带走。有几种可能的方法可以做到这一点，有些很奇特，有些更常见:

## 模块功能

```
module Sequences
  module Uuid
    module_function

    def company_uuid(n)
      "CMP-#{n}"
    end
  end
end

# Then you can use it either casting the method to proc after retrieving it with #method method or just calling it inside of the block.

FactoryBot.define do
  sequence(:company_uuid_1, &Sequences::Uuid.method(:company_uuid))
  sequence(:company_uuid_2) do |n|
    Sequences::Uuid.company_uuid(n)
  end
end 
```

注释:`module_function`在顶层的全局作用域中，基本上是用模块自身来扩展模块，并使所有的方法都是模块级和对象级的(如果模块包含在某个类中)。

## 类似 Proc 的物体

```
module Procable
  def to_proc
    Proc.new(&method(:call))
  end
end

module Sequences
  class CompanyUuid
    include Procable

    def call(n)
      "CMP-#{n}"
    end
  end
end

FactoryBot.define do
  sequence(:company_uuid, &Sequences::CompanyUuid.new)
end 
```

注释:`&something`基本上是那种情况下`#to_proc` - > convert 到 block 转换的替代语法。如果对象已经是一个 Proc，它就把它转换成 block。

## 带有 proc/lambda 常数的模块

```
module Sequences
  COMPANY_UUID_1 = -> n { "CMP-#{n}" } # lambda
  # or
  COMPANY_UUID_2 = Proc.new do |n|
    "CMP-#{n}"
  end
end

FactoryBot.define do
  sequence(:company_uuid, &Sequences::COMPANY_UUID_1)
end 
```

这些看起来很好，并且与测试工厂中的定义相分离，所以工厂本身看起来更像是声明性的。但这不是重点。转换的主要目的是使代码可测试。而现在是(因为是普通的红宝石)。你可以这样测试它:

```
 module Procable
    def to_proc
      Proc.new(&method(:call))
    end
  end

  class Sequences
    class CompanyUuid
      include Procable

      def call(n)
        n ** 2
      end
    end
  end

  describe Sequences::CompanyUuid do
    [
      1, 1,
      2, 4,
      3, 9,
      4, 16
    ].each_slice(2) do |input, output|
      it "is expected to eq #{output} on input #{input}" do
        expect(desdule Sequences
    COMPANY_UUID_1 = -> n { "CMP-#{n}" } # lam
  end 
```

旁注:如果这个文件夹是一个`spec/support`文件夹，最好不要把测试文件放在与序列代码相同的文件夹中。在大多数项目设置中，这个文件夹是在 RSpec 启动时加载的，因此您的序列测试将运行两次。如果不使用 RSpec 或 support folder，应该没问题。

## 后记

你的测试代码和你系统中的普通代码一样，是一个有尊严的公民，因为它会随着你的系统成长和变化，并确保你系统的一个重要品质——有效性和正常运行。它和你系统的其他部分在同样的压力下工作，并且必须尽可能地像系统的实际代码一样被干燥、分离和抽象。如果这种卫生行为没有很好地执行，您应该肯定会在您的系统开发的一个或另一个阶段遇到麻烦:脆弱的测试，大量注释掉的测试，以及在没有正确更新的错误和无用测试的沼泽中的整体下滑，所以它们变得无用。测试基础设施的一部分，可以在进入“Quis custodiet ipsos custodes”的无限循环之外进行测试(谁来看守望者？).生成代码为测试提供模拟数据。这段代码可以而且应该被测试，因为它是将您的测试套件绑定到现实的链接，并为它提供真实世界的数据。该序列是一种这样的代码，应该对它们进行测试，以避免出现我和其他许多人遇到的片状剥落问题。测试愉快！