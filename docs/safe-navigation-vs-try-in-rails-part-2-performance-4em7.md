# Rails 中的安全导航与尝试(第 2 部分:性能)

> 原文：<https://dev.to/jahboo/safe-navigation-vs-try-in-rails-part-2-performance-4em7>

本笔记是[安全导航与在 Rails 中尝试(第 1 部分:基本差异)](http://jetrockets.pro/til/posts/ats3ic6xvx-safe-navigation-vs-try-in-rails-part-1-basic-differences)的延伸

方法`try()`来自 Ruby on Rails 的主动支持组件。安全导航操作符是 Ruby 的固有特性。

让我们检查性能！

```
require 'benchmark'

class Foo
  attr_accessor :name
end

foo = Foo.new
bar = nil

Benchmark.bm(35) do |x|
  x.report('Successful access: try(...): ')         { 1_000_000.times { foo.try(:name) } }
  x.report('Successful access: &.: ')               { 1_000_000.times { foo&.name } }
  x.report('Successful access: control sample: ')   { 1_000_000.times { foo.name } }
  x.report('Failed access: try(...): ')             { 1_000_000.times { bar.try(:nonexistent) } }
  x.report('Failed access: safe navigation: ')      { 1_000_000.times { bar&.nonexistent } }
end;nil 
```

Enter fullscreen mode Exit fullscreen mode

```
 user     system      total        real
Successful access: try(...):          0.498216   0.005748   0.503964 (  0.530010)
Successful access: &.:                0.062146   0.000943   0.063089 (  0.069714)
Successful access: control sample:    0.062411   0.001098   0.063509 (  0.069603)
Failed access: try(...):              0.172535   0.004374   0.176909 (  0.194386)
Failed access: safe navigation:       0.054141   0.001029   0.055170 (  0.065502) 
```

Enter fullscreen mode Exit fullscreen mode

对于成功的导航，安全导航比方法`try()`快 7 倍，对于不成功的导航，安全导航比方法`try()`快 3 倍。