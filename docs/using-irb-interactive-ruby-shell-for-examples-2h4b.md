# 以 irb(交互式 Ruby Shell)为例

> 原文：<https://dev.to/burdettelamar/using-irb-interactive-ruby-shell-for-examples-2h4b>

像我们大多数人一样，我通常编写这样的代码示例:

*   将代码显示为突出显示的块。
*   (有时)将命令显示为高亮块。
*   将输出显示为突出显示的块。

最近，我一直在尝试使用交互式 Ruby Shell`irb`来构建示例。在`irb`中，我可以展示价值如何随着程序的进展而变化。

为了说明这一点，我将使用一个小的 Ruby 脚本来处理散列。

#### Ruby 会话输入

该代码在一个文件中，该文件将作为输入提供给`irb`。当代码执行时，它使用方法`Kernel#p`来显示和重新显示值，显示它们是如何变化的:

```
# Create and display two hashes.
h0 = {:a => 0, :b => 1}
p h0
h1 = {:b => 2, :c => 3}
p h1

# Merge the second hash into the first.
h0.merge!(h1)
p h0

# Add an element.
h0.store(:d, 4)
p h0

# Remove an element.
h0.delete(:b)
p h0 
```

#### 会话输出

下面是会话输出:

```
# Create and display two hashes.
h0 = {:a => 0, :b => 1}
p h0
{:a=>0, :b=>1}
h1 = {:b => 2, :c => 3}
p h1
{:b=>2, :c=>3}

# Merge the second hash into the first.
h0.merge!(h1)
p h0
{:a=>0, :b=>2, :c=>3}

# Add an element.
h0.store(:d, 4)
p h0
{:a=>0, :b=>2, :c=>3, :d=>4}

# Remove an element.
h0.delete(:b)
p h0
{:a=>0, :c=>3, :d=>4} 
```

#### 另一种疗法

上面代码中的“部分”很小，所以一次显示整个输出是有意义的。对于较大的段落，在代码和备注之间插入是有意义的。

我将重复上述步骤:

创建并显示两个哈希:

```
h0 = {:a => 0, :b => 1}
p h0
{:a=>0, :b=>1}
h1 = {:b => 2, :c => 3}
p h1
{:b=>2, :c=>3} 
```

将第二个散列合并到第一个:

```
h0.merge!(h1)
p h0
{:a=>0, :b=>2, :c=>3} 
```

添加一个元素:

```
h0.store(:d, 4)
p h0
{:a=>0, :b=>2, :c=>3, :d=>4} 
```

删除一个元素:

```
h0.delete(:b)
p h0
{:a=>0, :c=>3, :d=>4} 
```

#### Bash 命令

下面是完成这项工作的`bash`命令:

```
cat session_input.rb | irb > session_output.irb 
```

细目分类:

*   `cat`命令将 Ruby 文件`session_input.rb`放到`stdout`上。
*   然后，它作为输入被传送到`irb`。
*   来自`irb`的`stdout`流被重定向到文件`session_output.irb`。

#### 风雅

*   默认情况下，会话输出将回显 Ruby 输入的每一行，在这种情况下，大部分只是噪声。我使用选项`--noecho`来抑制回声。
*   会话输出以我们不需要(或不想要)的行`Switch to inspect mode.`开始。我使用`tail`命令来删除它:

完整命令:

```
cat session_input.rb | irb --noecho | tail +2 > session_output.irb 
```

#### Windows cmd 命令

```
type session_input.rb | irb --noecho > session_output.irb 
```

没有内置的实用程序(据我所知)。