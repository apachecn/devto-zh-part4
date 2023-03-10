# 猫鼬动态更新黑客

> 原文：<https://dev.to/rubiin/mongoose-dynamic-update-hack-21ad>

你说你为什么用猫鼬？让我们面对现实吧，编写 MongoDB 验证、转换和业务逻辑样板文件是一件很麻烦的事情。猫鼬缓解了整个混乱。
在使用 mongoose 专门进行更新操作时，您会注意到您必须为正在修改的文档提供选择器(通常是 _id 或用户名),以及您希望修改的字段和它们相应的值。
这里有一个简单的片段来总结上面的整个想法

```
User.update({
"username": req.params.user} , {$set:
{age: req.body.age, location: req.body.location, name:req.body.name ....}
} ,
function (err , success) {
if (err) throw (err);
else {
res.send({
msg: 'update success'
})
}}) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/8755e7e63511b45a5af995bcc22fa103.png)](https://i.giphy.com/media/mCRJDo24UvJMA/giphy.gif)

如果您要更新两个或几个字段，代码是没问题的，但是随着要更新的字段的增加，代码会变得有点难看。比如说 100 块地。此外，如果您设法为 100 个字段编写更新查询，模式中的属性名称将永远保持不变的可能性有多大。如果模式要更新，您将编写 100+100 = 200 个更新赋值。这就是我的代码要拯救的地方。它不仅动态填充查询中的更新字段，还从请求中获取字段名称。

```
const entries = Object.keys(req.body)
const updates = {}

// constructing dynamic query

for (let i = 0; i < entries.length; i++) {
updates[entries[i]] = Object.values(req.body)[i]
}
User.update({
"username": req.params.user
} , {
$set: updates
} ,
function (err , success) {
if (err) throw (err);
else {
res.send({
msg: "update success"
})
}
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/5e9dffd64f6b2d33d54e71b08387f955.png)](https://i.giphy.com/media/RyXVu4ZW454IM/giphy.gif)

快乐的节点

[![Beerpay](img/59a20ff435d3f302c36ec0a69fe33a00.png)](https://beerpay.io/rubiin/nest-easyconfigs)