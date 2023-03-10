# 所以您将一个转义字符串放入 JSON 列中...

> 原文：<https://dev.to/mrmurphy/so-you-put-an-escaped-string-into-a-json-column-2n67>

哎呦！假设我们不久前犯了一个错误，我们的应用程序将转义的 JSON 字符串写入 Postgres JSON 列，而不是 JSON 对象。我们现在不能使用任何 JSON 操作符来选择字段！我们该怎么办？

`select (ingredients #>> '{}')::jsonb->>'cheese' from pizza;`

做这个☝️.当您有一个带有 jsonb 列(配料)的表(比萨饼)时，您不小心在该列中放了一个转义的 JSON 字符串，而不是 JSON 对象。该值可能如下所示:

`"{\"cheese\": \"mozzarella\"}"`

看到报价了吗？这只是 JSON 列中根级别的一个字符串。但是你真正想要的是这样的:

`{"cheese": "mozzarella"}`

那更好。但是我们已经犯了错误，所以我们必须编写一个迁移来修复数据。我们是这样做的:

`(ingredients #>> '{}')::jsonb`

Postgres 中的`#>>`操作符获取“指定路径的 JSON 对象作为文本”([PostgreSQL:Documentation:9.3:JSON 函数和操作符](https://www.postgresql.org/docs/9.3/functions-json.html))。这里我们传入一个空路径，说我们希望 Postgres 给我们根级别的*未转义*字符串作为`text`值。

然后我们可以将这个`text`值转换回 JSON，如下所示:`(stand-in-for-text-value)::jsonb`。然后我们在根级别有一个普通的解析过的 JSON 对象，我们可以从中选择字段。