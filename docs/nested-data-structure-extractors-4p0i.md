# 嵌套数据结构提取器

> 原文：<https://dev.to/ohaddahan/nested-data-structure-extractors-4p0i>

想分享一些我用来从嵌套数据结构中遍历和提取数据的有用方法，通常是第三方 API 响应。

```
input_data = {
  x: {
    z: {
      first_name: 'hello',
      last_name: 'world'
    }
  },
  q: [
    1,
    2,
    {
      z: {
        first_name: 'hello',
        last_name: 'bye'
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

第一种提取方法`fetch_all_by_key`，这种方法的目的是提取所有具有相同关键字的元素(例如获取响应中相似对象的列表):

```
 def fetch_all_by_key(element: nil, key: nil, container: [])
  if element.is_a?(Array)
    element.each { |i| fetch_all_by_key(element: i, key: key, container: container) }
  elsif element.is_a?(Hash)
    container << element.fetch(key) unless element.fetch(key, nil).nil?
    element.each_pair { |_, v| fetch_all_by_key(element: v, key: key, container: container) }
  end
  container
end 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
fetch_all_by_key(element: input_data, key: :q)
[
    [0] [
        [0] 1,
        [1] 2,
        [2] {
            :z => {
                :first_name => "hello",
                 :last_name => "bye"
            }
        }
    ]
]

fetch_all_by_key(element: input_data, key: :z)
[
    [0] {
        :first_name => "hello",
         :last_name => "world"
    },
    [1] {
        :first_name => "hello",
         :last_name => "bye"
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

下一个方法与前一个方法基本相同，但是将只获取符合我们要求的第一个元素(当您只关心获取符合您的键的任何元素时):

```
 def deep_fetch(element: nil, key: nil)
  ret_value = tmp = nil
  if element.is_a?(Array)
    element.each do |i|
      tmp = deep_fetch(element: i, key: key)
      break unless tmp.nil?
    end
  elsif element.is_a?(Hash)
    if element.has_key?(key)
      tmp = element[key]
    else
      element.each_pair do |_, v|
        tmp = deep_fetch(element: v, key: key)
        break unless tmp.nil?
      end
    end
  end
  ret_value = tmp unless tmp.nil?
  ret_value
end

deep_fetch(element: input_data, key: :q)
[
    [0] 1,
    [1] 2,
    [2] {
        :z => {
            :first_name => "hello",
             :last_name => "bye"
        }
    }
]

 deep_fetch(element: input_data, key: :z)
{
    :first_name => "hello",
     :last_name => "world"
}
2.6.3 :096 
```

Enter fullscreen mode Exit fullscreen mode

最后一个方法使用前一个方法来加强顺序(有时同一个键可能出现在两个不同的层次路径中，因此您希望对获取的内容有更多的控制):

```
 def multi_deep_fetch(element: nil, keys: [])
  while key = keys.shift
    element = deep_fetch(element: element, key: key)
  end
  element
end

multi_deep_fetch(element: input_data, keys: [:q])
[
    [0] 1,
    [1] 2,
    [2] {
        :z => {
            :first_name => "hello",
             :last_name => "bye"
        }
    }
]

 multi_deep_fetch(element: input_data, keys: [:y, :z])
nil

 multi_deep_fetch(element: input_data, keys: [:x, :z])
{
    :first_name => "hello",
     :last_name => "world"
}

multi_deep_fetch(element: input_data, keys: [:q, :z])
{
    :first_name => "hello",
     :last_name => "bye"
} 
```

Enter fullscreen mode Exit fullscreen mode