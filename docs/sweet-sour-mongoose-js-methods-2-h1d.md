# 糖醋猫鼬. js 方法–2

> 原文：<https://dev.to/kauresss/sweet-sour-mongoose-js-methods-2-h1d>

[![Mongoose.js](img/ece87b398a3c559fa38566ed75ba2e5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vkqag35p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8o7x9tx9meytb7prody3.png)

这是“酸甜猫鼬. js 方法”系列的一部分。在这个函数中，我们将编写一个作用于 UserSchema 的方法来检查数据库中是否已经存在一个电子邮件或用户名。作为一个用例，这个方法可以在您的任何 routes 文件中使用，方法是在注册过程中导出“User”来检查电子邮件地址是否已经存在于数据库中。

模式静态方法直接作用于用户。在这种情况下，该方法称为“emailCheck”。您可能知道，方法是作用于对象的函数，在本例中是作用于用户模式的函数。该函数接受一个电子邮件地址和回调作为参数。对于任何特定的用户，findOne 方法用于将作为参数传递给“emailCheck”的电子邮件与数据库中的电子邮件地址进行匹配。所以这里的{email:email}是一个查询条件。除非使用“exec”执行，否则不会查询数据库。

```

UserSchema.statics.emailCheck = function(email, callback) {
  User.findOne({ email: email }).exec(function(error, user) {
    if (error) {
      return callback(error);
    }
    // Pass user to callback and handle whether email exist in the callback.
    callback(null, user);
  });
};

```