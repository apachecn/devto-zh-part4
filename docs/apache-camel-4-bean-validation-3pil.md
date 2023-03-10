# Apache Camel #4 - Bean 验证

> 原文：<https://dev.to/djoleb/apache-camel-4-bean-validation-3pil>

在很多情况下，我们需要为数据输入编写验证。这个“问题”可以通过使用 Bean 验证组件在 Camel 中轻松解决。

假设我们的路由中有一个 JSON 输入，我们想要验证有效负载。

```
{
     "name":"Jane Doe",
     "cardNumber":"377198642272436",
     "email":"jane@doe.com",
     "paid":true,
     "price":1.25
}

```

为此，我们首先必须将有效负载解组到 POJO。
但首先，我们必须创建一个与 JSON 具有相同参数的类，并添加 Hibernate 验证注释。

```
 @NotNull
 @Length(min = 1, max = 60)
 private String name;

 @CreditCardNumber
 private String cardNumber;

 @Email
 private String email;

 @NotNull
 private boolean paid;

 @Min(0.1)
 private int price; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们把这个加到我们的骆驼路线上。

```
   //define a datasource for the class you want to validate.
   GsonDataSource dataSource = new GsonDatasource(JsonDto.class);

   //unmarshal JSON to POJO
   .unmarshall(dataSource)

   //Validate data
   .to("bean-validator:someLabelHere)

```

当解组后的数据被发送到 bean validator 时，对象将由我们为 JsonDto 字段设置的注释进行验证。

感谢您的关注。