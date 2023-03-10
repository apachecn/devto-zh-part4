# 从散列中获取所有值的简单方法

> 原文：<https://dev.to/jetrockets/simple-way-to-get-all-values-from-hash-5hd1>

在 ruby from the box 中，如果它是嵌套的，我们无法找到所有的哈希值。我建议使用递归找到所有值的简单方法。

示例哈希:

```
hash = {
  a: 2,
  b: { c: 3, d: 4, e: {f: 5}}
} 
```

```
> hash.values
=> [2, {:c=>3, :d=>4, :e=>{:f=>5}}] 
```

那不是一个选项，我们需要所有的值。

```
def deep_values(array = [], object:)
  object.each do |_key, value|
    if value.is_a?(Hash)
      deep_values(array, object: value)
    else
      array << value
    end
  end
  array
end

> deep_values(object: hash)
=> [2, 3, 4, 5] 
```

如果您使用这些数据运行基准测试，我们会得到以下数据:

```
>  puts Benchmark.measure { 100_000.times { hash.values } }
=> 0.028920   0.002643   0.031563 (  0.032759)

>  puts Benchmark.measure { 100_000.times { deep_values(object: hash ) } }
=> 0.140439   0.003318   0.143757 (  0.146637) 
```