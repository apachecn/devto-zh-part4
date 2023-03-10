# 停止使用猫鼬的默认连接

> 原文：<https://dev.to/maixuanhan/stop-using-the-mongoose-s-default-connection-4nnj>

看看下面猫鼬使用的典型例子。

```
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/myapp', {useNewUrlParser: true});
var MyModel = mongoose.model('Test', new Schema({ name: String }));
// Works
MyModel.findOne(function(error, result) { /* ... */ }); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想建立另一种联系呢？或者如果我们想连接到另一个数据库？我们不能再用`mongoose.connect()`了，猫鼬不会知道我们要和哪个互动。永远不要考虑创建不同的模块来创建和使用分离的 mongose 对象，因为`require()`不是那样工作的，mongose 对象在第一次导入时被缓存。

连接对象用于创建和检索模型。模型的范围总是局限于单个连接。请注意，当我们调用`mongoose.connect()`时，mongoose 会创建一个默认连接。我们可以使用`mongoose.connection`访问默认连接。

不是每个人都注意到了。所以我的建议是我们避免使用`mongoose.connect()`。相反，我们使用`mongoose.createConnection()`。通过这种方式，我们可以为将来维护和扩展项目的其他开发人员节省大量时间。我们含蓄地要求他们意识到 mongoose 默认连接的事实。

上面的例子可以改写如下。

```
const mongoose = require('mongoose');
var connection = mongoose.createConnection('mongodb://localhost:27017/myapp',
                                           {useNewUrlParser: true});
var MyModel = connection.model('Test', new Schema({ name: String }));
// Works
MyModel.findOne(function(error, result) { /* ... */ }); 
```

Enter fullscreen mode Exit fullscreen mode

参考 API:[https://mongoosejs.com/docs/connections.html](https://mongoosejs.com/docs/connections.html)
* *本帖抄袭我的个人博客[https://rain forest 01 . blogspot . com/2019/08/stop-using-mongoses-default-connection . html](https://rainforest01.blogspot.com/2019/08/stop-using-mongooses-default-connection.html)