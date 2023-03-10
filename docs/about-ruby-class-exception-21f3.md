# 关于 Ruby 类异常

> 原文：<https://dev.to/burdettelamar/about-ruby-class-exception-21f3>

## 异常

### 有什么例外？

来自 Ruby 的`Exception`类的文档:

> 类 Exception 的后代用于在内核#raise 和`begin ... end`块中的`rescue`语句之间进行通信。异常对象携带关于异常的信息——异常的类型(异常的类名)、可选的描述性字符串和可选的回溯信息。

为了演示，我们将使用 Ruby 和 Ruby 交互 Shell，`irb`，从它们的版本:
开始

```
p `ruby --version`.chomp
"ruby 2.6.3p62 (2019-04-16 revision 67580) [x64-mingw32]"
p `irb --version`.chomp
"irb 1.0.0 (2018-12-18)" 
```

### 目录

*   [基础知识](#the-basics)
    *   [创建异常](#creating-exceptions)
    *   [检查异常](#examining-exceptions)
    *   [获救异常](#rescued-exceptions)
    *   [相等](#equality)
*   [更多方法](#more-methods)
    *   [方法:原因](#method-cause)
    *   [方法:to_tty？](#method-totty)
*   [全局变量](#global-variables)
*   [内置子类](#builtin-subclasses)
*   [自定义异常](#custom-exceptions)
*   [更](#more)

### 基础知识

#### 创建异常

创建新的例外:

```
x = Exception.new
p x
#<Exception: Exception> 
```

创建带有消息的新例外:

```
x = Exception.new('Boo!')
p x
#<Exception: Boo!> 
```

用非字符串参数创建一个新的异常(然后转换成字符串):

```
x = Exception.new(:symbol)
p x
#<Exception: symbol>
x = Exception.new(Range.new(0, 4))
p x
#<Exception: 0..4> 
```

`Exception.exception`的行为与`Exception.new`相同:

```
x = Exception.exception
p x
#<Exception: Exception>
x = Exception.exception('Boo!')
p x
#<Exception: Boo!> 
```

创建一个与现有异常类别相同的新异常，但使用不同的消息:

```
x = Exception.exception('x message')
p x
#<Exception: x message>
y = x.exception('y message')
p y
#<Exception: y message>
p x.__id__ == y.__id__
false 
```

方法`:exception`在没有参数传递时返回`self`:

```
x = Exception.new
p x
#<Exception: Exception>
y = x.exception
p y
#<Exception: Exception>
p x.__id__ == y.__id__
true 
```

方法`:exception`在将`self`作为参数传递时返回`self`:

```
x = Exception.new
p x
#<Exception: Exception>
y = x.exception(x)
p y
#<Exception: Exception>
p x.__id__ == y.__id__
true 
```

方法`:exception`返回一个新的异常，并带有一条新的消息，当传递其他任何东西作为参数时:

```
x = Exception.new
p x
#<Exception: Exception>
y = x.exception(:Boo)
p y
#<Exception: Boo>
p x.__id__ == y.__id__
false 
```

#### 检查异常

获取显示异常的类和消息的字符串:

```
x = Exception.new('Boo!')
p x.inspect
"#<Exception: Boo!>" 
```

获取异常消息:

```
p x.to_s
"Boo!" 
```

获取异常消息:

```
p x.message
"Boo!" 
```

对于没有消息的异常，方法`:message`返回类名。

```
x = Exception.new
p x.message
"Exception" 
```

#### 获救异常

救援例外:

```
rescued = nil
begin
    raise Exception.new('Boo!')
  rescue Exception => x
    rescued = x
  end 
```

显示其类别和消息:

```
p rescued.class
Exception
p rescued.message
"Boo!" 
```

方法`:backtrace`返回一个字符串数组。这个大:

```
backtrace = rescued.backtrace
p backtrace.class
Array
p backtrace.size
20 
```

整件事:

```
puts backtrace
irb_input:145:in `irb_binding'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `eval'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/context.rb:385:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:493:in `block (2 levels) in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:647:in `signal_status'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:490:in `block in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:246:in `block (2 levels) in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `loop'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `block in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:489:in `eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:428:in `block in run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:383:in `start'
C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/irb-1.0.0/exe/irb:11:in `<top (required)>'
C:/Ruby26-x64/bin/irb.cmd:31:in `load'
C:/Ruby26-x64/bin/irb.cmd:31:in `<main>' 
```

设置 backtrace，在本例中为空数组:

```
rescued.set_backtrace([])
puts rescued.backtrace 
```

最初的回溯信息仍然可以通过方法`:backtrace_locations`获得，但是结果是一个`Thread::Backtrace::Location`的数组，而不是一个`String`的数组。

```
p rescued.backtrace_locations.first.class
Thread::Backtrace::Location
puts rescued.backtrace_locations
irb_input:145:in `irb_binding'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `eval'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/context.rb:385:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:493:in `block (2 levels) in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:647:in `signal_status'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:490:in `block in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:246:in `block (2 levels) in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `loop'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `block in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:489:in `eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:428:in `block in run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:383:in `start'
C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/irb-1.0.0/exe/irb:11:in `<top (required)>'
C:/Ruby26-x64/bin/irb.cmd:31:in `load'
C:/Ruby26-x64/bin/irb.cmd:31:in `<main>' 
```

#### 相等

如果两个异常具有相同的类、消息和回溯，那么它们在`:==`下是相等的。

都一样:

```
clone = rescued.clone
p rescued.class == x.class
true
p rescued.message == x.message
true
p rescued.backtrace == x.backtrace
true
p rescued == clone
true 
```

不同类:

```
x = RuntimeError.new(rescued.message)
x.set_backtrace(rescued.backtrace)
p rescued.class == x.class
false
p rescued.message == x.message
true
p rescued.backtrace == x.backtrace
true
p rescued == x
false 
```

不同的消息:

```
x = rescued.exception('Foo!')
x.set_backtrace(rescued.backtrace)
p rescued.class == x.class
true
p rescued.message == x.message
false
p rescued.backtrace == x.backtrace
true
p rescued == x
false 
```

不同回溯:

```
x = clone.exception
backtrace = rescued.backtrace_locations.map { |x| x.to_s }
x.set_backtrace(backtrace)
p rescued.class == x.class
true
p rescued.message == x.message
true
p rescued.backtrace == x.backtrace
false
p rescued == x
false 
```

### 更多方法

#### 方法:原因

当一个异常被引发时，方法`:cause`返回先前引发的异常，如来自[斯塔尔·霍恩](https://www.honeybadger.io/blog/nested-errors-in-ruby-with-exception-cause/) :
的这段代码所示

```
def fail_and_reraise
    raise NoMethodError
  rescue
    raise RuntimeError
  end

begin
    fail_and_reraise
  rescue => e
    puts "#{ e } caused by #{ e.cause }"
  end
RuntimeError caused by NoMethodError 
```

#### 方法:to_tty？

确定是否将异常写入 tty:

```
p Exception.to_tty?
false 
```

### 全局变量

在`rescue`、`ensure`、`at_exit`或`END`块中，已经引发但尚未处理的异常可通过全局变量访问。`$!`有引发的异常，`$@`有回溯

```
begin
    raise Exception.new('Boo!')
  rescue Exception => x
    p $!
    puts $@
  end
#<Exception: Boo!>
irb_input:267:in `irb_binding'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `eval'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/workspace.rb:85:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/context.rb:385:in `evaluate'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:493:in `block (2 levels) in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:647:in `signal_status'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:490:in `block in eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:246:in `block (2 levels) in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `loop'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:232:in `block in each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb/ruby-lex.rb:231:in `each_top_level_statement'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:489:in `eval_input'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:428:in `block in run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `catch'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:427:in `run'
C:/Ruby26-x64/lib/ruby/2.6.0/irb.rb:383:in `start'
C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/irb-1.0.0/exe/irb:11:in `<top (required)>'
C:/Ruby26-x64/bin/irb.cmd:31:in `load'
C:/Ruby26-x64/bin/irb.cmd:31:in `<main>' 
```

### 内置子类

参见`Exception`类的内置子类，在[它的文档](https://ruby-doc.org/core-2.6.3/Exception.html)。

### 自定义异常

许多 ruby 爱好者认为，使用定制异常而不是内置异常是一个好习惯。阅读所有相关信息:

*   [自定义异常](https://www.google.com/search?q=ruby+custom+exceptions)

### 更

*   [文档](https://ruby-doc.org/core-2.6.3/Exception.html)
*   [源代码](https://github.com/ruby/ruby/blob/master/error.c)
*   [相关宝石](https://rubygems.org/search?query=exception)
*   [最佳实践](https://www.google.com/search?q=ruby+exceptions+best+practice)
*   [性能](https://www.google.com/search?q=ruby++exception+performance)