# ruby 上的枚举分区

> 原文：<https://dev.to/webcoderph/enum-partition-on-ruby-6id>

```
data = [1,2,4,5,6,7,8,9,10]

odd, even = data.partition(&:odd?)

=>[[1, 5, 7, 9], [2, 4, 6, 8, 10]] 
```

*这只是一个示例。