# 用 WordPress REST API 写入终极字段的重复字段

> 原文：<https://dev.to/blueset/write-to-repeater-fields-of-ultimate-fields-with-wordpress-rest-api-1n5j>

Ultimate Fields 是一个 WordPress 插件，可以为现有的文章类型添加自定义字段，包括内置和自定义字段。这是我发现的唯一一个可以添加重复字段的免费插件。然而，这个插件几乎一年没有更新，并且有一个无法使用 REST API 编写 repeater 字段的 bug。

我只是试图用一个 JSON 对象数组来更新这个字段，得到了这个错误消息`[FIELD_NAME] is not of type 'string'`。在深入研究了源代码之后，我发现 REST API 适配器正在检查每个字段类是否有定义的 JSON 模式类型。当找不到该类型时，它将回退到 string。

令人惊讶的是，`get_api_data_type`在代码中除了上面的几行之外，其他地方都没有使用，而显然 repeater 字段应该采用数组类型。

所以现在解决这个问题很简单。我需要做的就是添加返回`"array"`到`ultimate-fields/core/classes/Field/Repeater.php`的方法。

```
public function get_api_data_type() { return "array"; } 
```

这样，REST API 会很高兴地将 repeater 字段输入正确地识别为一个列表，并按预期写入数据库。

帖子[用 WordPress REST API](https://blog.1a23.com/2019/09/17/write-to-repeater-fields-of-ultimate-fields-with-wordpress-rest-api/) 写终极字段的重复字段最早出现在 [1A23 博客](https://blog.1a23.com)上。