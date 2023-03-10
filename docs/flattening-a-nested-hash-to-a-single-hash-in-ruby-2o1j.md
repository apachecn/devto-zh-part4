# 在 Ruby 中将嵌套散列展平为单个散列

> 原文：<https://dev.to/danimal141/flattening-a-nested-hash-to-a-single-hash-in-ruby-2o1j>

当使用 Ruby 时，你有时可能需要展平一个嵌套散列，比如:

```
{
  :foo => "bar",
  :hello => {
    :world => "Hello World",
    :bro => "What's up dude?",
  },
  :a => { :b => { :c => "d" } }
} 
```

Enter fullscreen mode Exit fullscreen mode

到单个散列，如:

```
{
  :foo => "bar",
  :"hello.world" => "Hello World",
  :"hello.bro" => "What's up dude?",
  :"a.b.c" => "d"
} 
```

Enter fullscreen mode Exit fullscreen mode

今天，我介绍一种参考堆栈溢出中的 [a post 来实现它的方法。](https://stackoverflow.com/questions/23521230/flattening-nested-hash-to-a-single-hash-with-ruby-rails)

## 让我们实施吧

```
def flatten_hash_from(hash)
  hash.each_with_object({}) do |(key, value), memo|
    next flatten_hash_from(value).each do |k, v|
      memo["#{key}.#{k}".intern] = v
    end if value.is_a? Hash
    memo[key] = value
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

递归地使用`Enumberable#each_with_object`有助于我们实现它。
让我们深入了解它的工作原理。

## 工作原理

1.  首先，`key = :foo`，`value = "bar"`给出。这个值不是一个散列，所以`memo`变成了`{ :foo => "bar" }`，这个过程继续。

2.  `key = :hello`，`value = { :world => "Hello World", :bro => "What's up dude?" }`给出。该值是一个散列值，所以用该值再次调用`flatten_hash_from`。

3.  步骤 2 之后，`memo`又是`{}`，和步骤 1 中的不一样。然后，`key = :world`，`value = "Hello World"`给定，这不是哈希，所以`memo`变成了`{ :world => "Hello World" }`。类似地，接下来给出`key = :bro`，然后`memo`变成`{ :world => "Hello World", :bro => "What's up dude?" }`，结果返回到步骤 2。

4.  从步骤 3 开始，过程返回到带有`key = :hello`的`each`部分。由`memo["#{key}.#{k}".intern] = v`，`memo`变成了`{ :foo => "bar", :"hello.world" => "Hello World", :"hello.bro" => "What's up dude?" }`。

5.  接下来，`key = :a`，`value = { :b => { :c => "d" } }`给出。值是一个散列，然后像步骤 2 一样递归调用`flatten_hash_from`。

6.  `flatten_hash_from`是用`key = :b`、`value = { :c => "d" }`调用的。

7.  `{ :c => "d" }`返回到步骤 6。

8.  `{ :"b.c" => "d" }`返回到步骤 5。

9.  从步骤 5 开始，过程返回到带有`key = :a`的`each`部分，并且`{ :"a.b.c" => "d" }`被添加到`memo`。原来`memo`是`{ :foo => "bar", :"hello.world" => "Hello World", :"hello.bro" => "What's up dude?", :"a.b.c" => "d" }`，然后流程完成。

## 参考文献

*   [https://stack overflow . com/questions/23521230/flattening-nested-hash-to-single-hash-with-ruby-rails](https://stackoverflow.com/questions/23521230/flattening-nested-hash-to-a-single-hash-with-ruby-rails)