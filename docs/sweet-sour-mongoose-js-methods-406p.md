# 糖醋猫鼬. js 方法

> 原文：<https://dev.to/kauresss/sweet-sour-mongoose-js-methods-406p>

[![Alt Text](img/7fe9d30e468ffb6de497e9542b2a9493.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j6T3Cr0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7stgzy0pd027w7ssm85k.png)

我一直在使用 mongose(MongoDB 的 ODM 库)进行认证项目。像 MongoDB 这样的 NoSQL 数据库对于依赖用户交互的 web 应用程序来说确实更灵活。例如，用户可能决定使用社交媒体帐户登录，或者使用他们的电子邮件、密码和机密问题答案进行注册。

我发现编写一个 mongoose 方法来处理每次用户决定做一些不同的事情时更新 app.js、user.js、routes.js 要容易得多！

下面是一个简短的 mongoose.js 方法，它是一个预保存钩子。它将检查您的模式中是否存在用户名。如果用户名存在，它将返回一个错误，如果不存在，那么该方法将有助于将用户名保存到 db:该方法本质上是用户模式的一个函数。

```
UserSchema.pre("save", function(next) {
    const self = this;
    User.find({
        name: self.name
    }, function(err, docs) {
        if (!docs.length) {
            next();
        } else {
            console.log("user exists: ", self.name);
            next(new Error("User exists!"));
        }
    });
});

```

ps:我会继续编辑这个文档，添加更多的功能