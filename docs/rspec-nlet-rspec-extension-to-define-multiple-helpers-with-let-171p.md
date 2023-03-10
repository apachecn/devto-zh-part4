# rspec-nlet:用 let 定义多个助手的 rspec 扩展

> 原文：<https://dev.to/masakazutakewaka/rspec-nlet-rspec-extension-to-define-multiple-helpers-with-let-171p>

# rspec-nlet

有一天，我在测试一些我用 Ruby 构建的命令行工具，我试着写了一个这样的测试代码。

```
let(:msg, :status) { Open3.capture2e(command) } 
```

但是我意识到 RSpec 不允许你用`let`一次定义多个助手。

所以我为它创造了一个宝石！ [rspec-nlet](https://github.com/masakazutakewaka/rspec-nlet)
我知道这个宝石的使用案例很少哈哈。

# 用法

您可以一次定义一个或多个助手。如果块中的名称和返回值不匹配，就会出现错误。

详情可以阅读[自述](https://github.com/masakazutakewaka/rspec-nlet#usage)。

```
# define multiple helpers
> let(:one, :two, :three) { [1,2,3] }
> one # => 1
> two # => 2
> three # => 3

# define a helper
> let(:single) { 'single' }
> single # => 'single'

# raise an error when too few names are passed
> let(:too, :few) { [1,2,3] }
> few # => StandardError (Too few names)

# raise an error when too many names are passed
> let(:too, :many) { [1] }
> many # => StandardError (Too many names) 
```

## 注

这个特性实际上是过去向 rspec-core 提出的。

[https://github.com/rspec/rspec-core/issues/1650](https://github.com/rspec/rspec-core/issues/1650)

但是由于几个原因它被拒绝了:

*   `lets`不是一个好名字
*   没有那么多用例
*   处理角落案例

但是它可以像宝石一样生活在外面！

感谢您的阅读！

## 参考文献

*   [https://github.com/rspec/rspec-core/issues/1650](https://github.com/rspec/rspec-core/issues/1650)