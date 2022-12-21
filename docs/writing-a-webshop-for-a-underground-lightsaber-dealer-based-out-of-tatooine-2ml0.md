# 为塔图因的地下光剑经销商写网店

> 原文：<https://dev.to/itminds/writing-a-webshop-for-a-underground-lightsaber-dealer-based-out-of-tatooine-2ml0>

在九月底，IT-Minds 将举办一个关于阿波罗登月的研讨会。在这个工作坊中，我们将为沃图建立一个网上商店，他是一个著名的塔图因奴隶贩子。

沃图非常突然地发现了一些光剑零件，他正试图用这些零件来保护一家雇佣你建造的网店。

首先，我们设计所需的功能。

## 模型

由于我们将与用户和交易打交道，我们需要某种可交易的项目。这个特定的网店将只处理商品的*交易*。

一把光剑可以被设计成:

```
model Item {
  id                 String     @default(cuid()) @id @unique
  saberPart          SaberParts
  partName           String
  partDescription    String?
  price              Float?
}

enum SaberParts {
  CRYSTAL
  POWER_CORE
  CRYSTAL_VIBRATOR
  HILT
} 
```

这些物品将在交易中进行交易。这些交易将有一个买方和一个卖方。交易对象将充当双方的销售清单。

此外，我们需要一个用户，将持有他们所做的销售和购买的参考。

目前为止还不错。

## 后台模式

在创建了底层数据结构之后，我们需要找到向我们的网上商店公开这些数据的最佳方式。

首先我们需要用户。这些用户应该能够注册和注册后登录。为了简洁起见，用户仅由唯一的用户名指定。

为了构建我们的应用模式，我们将使用 [prisma-nexus](https://nexus.js.org/docs/database-access-with-prisma-v2) 。Prisma nexus 是一个代码优先的应用程序模式创建工具。

GraphQL 模式的根类型将是`{Mutation: {...}, Query: {...}, Subscription: {...} }`。

订阅部分被省略了，因为它被用来实现一个类似 websocket 的接口，并且对于这个特定的地下网络商店是不需要的。

为了能够在我们的应用模式上指定不同的变化和查询，GraphQL 需要知道它将/能够返回哪种类型。

让我们实现我们的项目模型在我们的应用程序模式上实现时的样子:

```
const Item = objectType({
  name: 'Item',
  definition(t) {
    t.model.id();
    t.field('saberPart', {
      type: 'saberPart',
    });

    t.model.partDescription();
    t.model.partName();
  },
}); 
```

在这段代码中，我们指定在我们的模式中存在一个名为`'Item'`的 objectType。这种类型的定义在 definition 函数中指定。

因为我们已经使用 prisma nexus 编写了我们的模式，所以我们可以在`t`上使用一个名为`model`的字段。这将获取模型中定义的值，并将其放在 appschema 上。当使用 typescript 时，您甚至将拥有针对这些类型的智能感知。

定义新类型时，使用`t.field`。第一个字符串是字段的名称，后面是类型。正如我们从前面显示的数据库模式中所记得的，`saberPart`是一个枚举类型。

我们已经把模型记下来了，现在来改变它！

这是我们将在 GraphQL 模式的查询和变异部分定义键的地方。让我们从如何创建一个项目开始。

```
const ItemArgs = inputObjectType({                                                                                                                             
  name: 'ItemArgs',                                                                                                                                            
  definition: t => {                                                                                                                                           
    t.string('partDescription', { required: true });
    t.string('partName', { required: true });
    t.field('saberPart', { type: 'saberPart', required: true });
  },
});

t.field('itemCreate', {
  type: 'Item',
  args: { data: arg({ type: ItemArgs, required: true }) },
  resolve: (_, { data }, ctx) => {
    const { partName, partDescription, saberPart } = data;
    return ctx.photon.items.create({
      data: {
        partName,
        partDescription,
        isAvailableLocally: true,
        saberPart,
      },
    });
  },
}) 
```

上面显示的`t.field`是一个名为 Mutation 的类型的定义字段，就像 Item 类型一样定义。

然而，这些字段将控制这个`mutation`将具有什么参数。上面的变异将有一个名为`data`的参数，它属于`ItemArgs`类型。

最后，在解析器中，实际的魔法正在发生，这里我们从参数中取出`data`，ApolloServer 将把`ctx`注入到每个解析器中，我们可以创建我们的类型。Photon 是一个自动生成的对象，作为我们数据库的接口。

`ctx.photon.items.create`返回一个承诺，但是 ApolloServer 足够聪明，知道在将数据返回给客户机之前必须等待数据。

## 前端！

我们有底层模型！我们必须应用模式，让我们使用它！

```
mutation  {  itemCreate(  data:  {  partDescription:  "This is true"  partName:  "Major Hilt saber"  saberPart:  CRYSTAL_VIBRATOR  }  )  {  id  }  } 
```

首先，我们指定这是一个变异，要调用的变异将是 itemCreate。我们按照上面的模型指定数据参数。

当我们完成这个项目时，假设我们对新项目不感兴趣，我们只想知道这个项目是否正确地添加到数据库中。因此我们只要求得到`id`作为回报。然而，如果您想要数据作为回报，您只需指定它。

在这个非常小的物体的情况下，它不会产生很大的差异。但是想象一下你有一个巨大的类型，有 37 个字段的大量数据。在这里，您可以选择想要的确切数据，或者只是 id，以确保请求完成。

上述查询的响应将是一个 json 对象:

```
{  "data":  {  "itemCreate":  {  "id":  "cjzxl45pr00000bva21oz2my4"  }  }  } 
```

注意响应是如何匹配上面的查询的，这是 GraphQL 的另一个伟大特性。

如果你住在丹麦的奥尔胡斯，你可以免费加入这个工作坊，方法是到:[https://www.facebook.com/events/397576180864407/](https://www.facebook.com/events/397576180864407/)，否则我会在 23 日之后发布工作坊的 Github 网址和自述。

如果你有问题，不要犹豫，在下面留下你的评论，或者只是表达你的爱。快乐编码。