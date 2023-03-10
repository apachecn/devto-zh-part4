# 通过 MongoDB 服务器会话使用 NestJS 在 GraphQL API 中实现因果一致性

> 原文：<https://dev.to/nickylenaers/achieving-causal-consistency-in-your-graphql-api-using-nestjs-with-mongodb-server-sessions-3go1>

*“现在实现什么了？!"*

在本文中，我们将关注如何实现**因果一致性**，这是一个捕捉软件系统中操作之间因果关系的概念。我们将通过查看带有 [GraphQL](https://graphql.org/) 端点的[节点](https://nodejs.org) API 的代码示例来做到这一点。我们将使用 [NestJS](https://nestjs.com/) 作为我们的 API 框架，使用[mongose](https://mongoosejs.com/)作为我们的 MongoDB 适配器。在本文结束时，您将:

*   熟悉因果一致性的概念
*   能够认识到因果一致性的必要性
*   能够使用现代技术堆栈构建节点实现

# 因果一致性

**因果一致性**的概念抓住了操作之间的因果关系。这意味着，如果我们有一些操作`A`导致*操作*操作`B`，因果一致性提供了操作顺序的保证，而不管它们运行在当前的进程中。如果我们不将这种保证落实到位，流程可能无法就执行顺序达成一致。例如，我们可能有一个在方`A`和方`B`之间转账的应用程序，转账从`A`到`B`。汇款由两部分组成:

1.  从银行账户取钱`A`
2.  将钱存入银行账户`B`

除了这些步骤的顺序之外，重要的是，如果这些操作中的任何一个失败，则另一个应该无效。例如，假设只有步骤 1 成功。然后我们从`A`处取钱，但`B`从未收到，反之亦然。

在接下来的几节中，我们将看一个由一组需求定义的简单用例，以及一些用于实现因果一致性的技术。

# 🍕用例

出于演示的目的，我们采用一个简单的 pizza delivery 节点 API，它有一个名为`orderPizza`的 GraphQL 端点。前端 web 应用程序使用该端点来订购比萨饼。一旦用户点击了端点，API 要求声明应该发生两件事情:

1.  在`pizza-orders`集合中创建新的订单文档。
2.  在`users`集合中添加对请求用户文档的订单引用。

    后者将用于高效查询每个用户的订单，因为我们希望避免每次用户请求自己的订单时扫描整个`pizza-orders`集合。这个需求很重要，因为潜在的动机揭示了应该在 API 中强制执行的检索每用户订单的*数据访问模式*。此外，要求指出:

3.  操作应**因果一致**。换句话说:按顺序执行，如果一个操作失败，其他操作应该中止。

既然我们已经建立了需求，我们可以开始考虑实现了。

# 雀巢公司

NestJS 是最健壮的节点 API 框架之一。它提供了一个强大的模块化系统，以及像 TypeScript 支持和依赖注入这样的特性，就像在 Angular 这样的框架中看到的那样。使用 MongoDB 的典型现代 API 可能会达到这样一个程度，即需要在一个操作中更新*多个文档，尽管数据结构可能不提供这样的原子性。相反，执行多文档写操作的挑战出现了。尽管这些操作本身都是原子性的，但操作作为一个整体却不是。这可能会导致我们的数据更新不一致，因为我们的数据可能与**有因果关系**。这就是 [MongoDB 服务器会话](https://docs.mongodb.com/manual/reference/method/Session/)的用武之地。幸运的是，NestJS 提供了[拦截器](https://docs.nestjs.com/interceptors)和[定制装饰器](https://docs.nestjs.com/custom-decorators)的概念，以一种简洁的方式集成 MongoDB 服务器会话。*

# MongoDB 服务器会话

MongoDB 服务器会话的概念与 mongose 中的 [`Transactions`](https://mongoosejs.com/docs/transactions.html) 有些类似，因为 mongose 事务是建立在 MongoDB 服务器会话之上的。来自[官方文件](https://docs.mongodb.com/manual/reference/server-sessions/):

> MongoDB 的服务器会话或逻辑会话是客户端会话用来支持因果一致性和可重试写入的底层框架。

请注意，MongoDB 服务器会话有其局限性。再次来自[官方文件](https://docs.mongodb.com/manual/reference/server-sessions/):

> *服务器会话仅适用于副本集和分片集群。*

如果在本地环境中设置 MongoDB 副本集有问题，可以使用 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 设置一个免费的 MongoDB 集群。

# 实现时间

## NestJS 猫鼬& GraphQL 设置

为了实现我们的 API 需求，我们从利用 [`@nestjs/graphql`](https://www.npmjs.com/package/@nestjs/graphql) 模块开始。它为 GraphQL 端点的快速实现提供了一些很好的抽象。此外，我们将使用取自 [`@nestjs/mongoose`](https://www.npmjs.com/package/@nestjs/mongoose) 的`MongooseModule`进行数据库交互。我们首先将这些模块添加到我们的主要 NestJS 应用程序模块中:

#### **`app.module.ts`T4】**

```
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { MongooseModule } from '@nestjs/mongoose';

/**
 * You can use your own connection string for connecting
 * to a MongoDB Replica Set using the `forRoot` method of
 * the `MongooseModule`.
 */
@Module({
  imports: [
    MongooseModule.forRoot('mongodb+srv://user:password@cluster/database'),
    GraphQLModule.forRoot()
  ]
})
export class AppModule {} 
```

按照 NestJS 的方法，我们的下一个目标是创建一个`PizzaOrder`模块，在其中我们可以定义我们的 MongoDB `PizzaOrderSchema`、GraphQL `PizzaOrderResolver`和`PizzaOrderService`。使用基于特征的文件和文件夹结构，我们需要这样的东西:

```
.
📁 pizza-order/
  +-- 📁 models/
    +-- pizza-order.model.ts
  +-- 📁 schemas/
    +-- pizza-order.schema.ts
  +-- pizza-order.module.ts
  +-- pizza-order.resolver.ts
  +-- pizza-order.service.ts
+-- app.module.ts 
```

这种方法可以很好地分离关注点，并在 API 的不同层之间划分职责。`PizzaOrderModule`看起来会像这样:

#### **`pizza-order.module.ts`T4】**

```
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { PizzaOrderResolver } from './pizza-order.resolver';
import { PizzaOrderService } from './pizza-order.service';
import { PizzaOrderSchema } from './schemas/pizza-order.schema';

/**
 * Mongoose automatically registers a Model based
 * on the provided Schema using the `forFeature`
 * method on the `MongooseModule`.
 */
@Module({
  imports: [
    MongooseModule.forFeature([
      {
        name: 'PizzaOrder',
        schema: PizzaOrderSchema,
        collection: 'pizza-orders'
      }
    ])
  ],
  providers: [PizzaOrderResolver, PizzaOrderService]
})
export class PizzaOrderModule {} 
```

现在我们需要提供实际的 GraphQL 端点。我们通过定义我们的 GraphQL 解析器来做到这一点:

#### **`pizza-order.resolver.ts`T4】**

```
import { Args, Mutation, Resolver } from '@nestjs/graphql';
import { User } from '@pizza-api/user/models/user.model';
import { CurrentUser } from '@pizza-api/user/decorators/current-user';
import { PizzaOrderInput } from './dto/pizza-order.input';
import { PizzaOrder } from './models/pizza-order.model';
import { PizzaOrderService } from './pizza-order.service';

/**
 * The GraphQL Resolver takes care of all resolver-related
 * responsibilities. It should therefore **never** perform
 * any database interactions directly.
 */
@Resolver(() => PizzaOrder)
export class PizzaOrderResolver {
  /**
   * We inject the `PizzaOrderService`, which is used to
   * interact with MongoDB through Mongoose.
   */
  constructor(private pizzaOrderService: PizzaOrderService) {}

  /**
   * The mutation that our API should perform, based
   * on the requirements.
   */
  @Mutation()
  public orderPizza(
    @CurrentUser() currUser: User,
    @Args() newOrder: PizzaOrderInput
  ) {
    /**
     * This seems like a good place for our requirements:
     *
     * 1\. Create a new order document in a `pizza-orders` collection.
     * 2\. Add an order reference to the requesting user document in a `users` collection.
     * 3\. The operations should be **causally consistent**.
     */
  }
} 
```

最后，我们有通过 Mongoose 处理数据库交互的服务:

#### **`pizza-order.service.ts`T4】**

```
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { UserService } from '@pizza-api/user/user.service';
import { Document, Model } from 'mongoose';
import { PizzaOrder } from './interfaces/pizza-order';
import { PizzaOrderInput } from './dto/pizza-order.input';

/**
 * The Service is used to provide a layer through which
 * database interactions ought to be performed. Results are
 * then passed back to the GraphQL Resolver for optional
 * further processing.
 */
@Injectable()
export class PizzaOrderService {
  /**
   * The injected `UserService` is used to perform
   * a mutation on the User's document for adding a
   * reference to a Pizza Order.
   */
  constructor(
    @InjectModel('PizzaOrder')
    private pizzaOrderModel: Model<PizzaOrder & Document>,
    private userService: UserService
  ) {}

  public createOrder(userId: string, newOrder: PizzaOrderInput) {
    /**
     * This is the place where the actual creation
     * of the document takes place:
     *
     * 1\. Create a new order document in a `pizza-orders` collection.
     */
  }

  public addOrderRefToUser(userId: string, orderId: string) {
    /**
     * This is where we add the order to the user:
     *
     * 2\. Add an order reference to the requesting user document in a `users` collection.
     */
  }
} 
```

## NestJS 拦截器&参数装饰器

既然已经设置了披萨订单功能，我们继续使用 NestJS 拦截器和定制装饰器。NestJS 中的拦截器受到了[面向方面编程](https://en.wikipedia.org/wiki/Aspect-oriented_programming)技术的启发。使用拦截器的原因之一是在方法执行之前或之后绑定额外的逻辑。当我们考虑我们的需求时，我们看到这个概念适合我们的用例:

> 我们希望在 GraphQL 解析器方法执行之前开始会话，在方法执行之后结束会话。

使用这种方法，我们可以为每个请求创建一个会话，并将其用于该请求中的所有数据库交互。因此，管理会话的责任将被分配给 GraphQL 解析器。我们将扩展我们的文件结构如下:

```
📁 decorators/
  +-- mongo-session.decorator.ts
📁 interceptors/
  +-- mongo-session.interceptor.ts
📁 pizza-order/
  +-- ...
+-- app.module.ts 
```

这就把我们带到了 NestJS 拦截器的实现:

#### **`mongo-session.interceptor.ts`T4】**

```
import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor
} from '@nestjs/common';
import { GqlExecutionContext } from '@nestjs/graphql';
import { InjectConnection } from '@nestjs/mongoose';
import { Connection } from 'mongoose';
import { from, Observable, throwError } from 'rxjs';
import { catchError, mapTo, switchMap, tap } from 'rxjs/operators';

export const MONGO_SESSION_KEY = 'MONGO_SESSION_KEY';

@Injectable()
export class MongoSessionInterceptor implements NestInterceptor {
  /**
   * A Mongoose Connection is injected, so we can use
   * it to create and start a session.
   */
  constructor(@InjectConnection() private readonly connection: Connection) {}

  /**
   * The `intercept` method return a Promise, since we
   * are `await`-ing the `startSession` method on the
   * connection. The returned Promise holds an Observable
   * we use to pipe different operators together.
   */
  async intercept(
    context: ExecutionContext,
    next: CallHandler
  ): Promise<Observable<any>> {
    const graphQlCtx = GqlExecutionContext.create(context);
    const ctx = graphQlCtx.getContext();
    const session = await this.connection.startSession();

    /**
     * We assign the newly created session to the
     * the GraphQL context, which allows us to access
     * the session later on via a `Custom Decorator`.
     */
    ctx[MONGO_SESSION_KEY] = session;
    session.startTransaction();

    /**
     * A chain of RxJS Observables is used to `pipe` the
     * operations together. In the end, we either commit
     * or abort the transactions, followed by a final statement
     * to end the transaction. 
     */
    return next.handle().pipe(
      switchMap(data =>
        from(
          session.inTransaction()
            ? session.commitTransaction()
            : Promise.resolve()
        ).pipe(mapTo(data))
      ),
      tap(() => session.inTransaction() && session.endSession()),
      catchError(async err => {
        if (session.inTransaction()) {
          await session.abortTransaction();
          session.endSession();
        }

        throw err;
      })
    );
  }
} 
```

在上面的代码中，我们有一些重要的步骤来使拦截器按照预期的方式运行:

*   `startSession()`方法是异步的，所以我们需要在那里使用`await`。
*   由于`commitTransaction()`返回`void`，我们使用一个`mapTo()`操作符来返回请求中传递的实际数据。
*   `catchError()`回调是异步的，因为我们需要在`abortTransaction()`方法返回一个`Promise`时对其进行`await`。
*   在`catchError()`操作符的末尾，被捕获的错误**被再次抛出**。这是需要注意的重要一点，因为我们希望我们的拦截器遵守[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle) (SPR)。因此，任何错误的转换或格式化都应该放在一个(另一个)专用的拦截器中。
*   我们确保，在提交或中止事务之前，我们使用`inTransaction()`方法检查会话是否确实在事务的*中。*

我们现在需要做的最后一件事是实现自定义装饰器，以便访问我们在`MongoSessionInterceptor`中创建的会话，因为我们需要访问我们的 GraphQL 解析器中的会话。因此，我们如下实现了`MongoSession`装饰器:

#### **`mongo-session.decorator.ts`T4】**

```
import { createParamDecorator } from '@nestjs/common';
import { CustomParamFactory } from '@nestjs/common/interfaces';
import { GraphQLResolveInfo } from 'graphql';
import { MONGO_SESSION_KEY } from '../interceptors/mongo-session.interceptor';

export const mongoSessionFactory: CustomParamFactory = (
  data: any,
  [root, args, ctx, info]: [object, any, any, GraphQLResolveInfo]
) => ctx[MONGO_SESSION_KEY];

export const MongoSession = createParamDecorator(mongoSessionFactory); 
```

> 您可能会注意到，上面的工厂也可以直接在`createParamDecorator()`中实现，然而，这会使测试工厂变得更加困难。因此，我们在这里将二者分开。

我们现在准备开始使用我们在`PizzaOrderModule`和 friends 中的实现。

# 用法

现在我们已经有了客户端使用的所有实现，我们可以开始向 GraphQL 解析器添加拦截器和定制装饰器了:

#### **`pizza-order.resolver.ts`T4】**

```
import { ClientSession } from 'mongoose';
import { MongoSession } from '../decorators/mongo-session.decorator';
// ...

@Resolver(() => PizzaOrder)
export class PizzaOrderResolver {

  // ...

  /**
   * We can now start using our `MongoSession` decorator
   * inside this endpoint and pass that `session` along
   * with our service calls.
   */
  @Mutation()
  public async orderPizza(
    @CurrentUser() currUser: User,
    @Args() newOrder: PizzaOrderInput,
    @MongoSession() session: ClientSession
  ) {
    const order = await this.pizzaOrderService.createOrder(currUser.id, newOrder, session);
    await this.pizzaOrderService.addOrderRefToUser(currUser.id, order.id);

    return { success: true };
  }
} 
```

现在需要更改服务方法的签名，以便接受会话参数。然后，我们可以使用该会话进行进一步的数据库交互:

```
// ...

@Injectable()
export class PizzaOrderService {

  // ...

  /**
   * We now use the `session` as an argument to
   * the `save()` method on a document. This ensures
   * that the operation becomes part of the current
   * transaction.
   */
  public createOrder(
    userId: string, 
    newOrder: PizzaOrderInput, 
    session: ClientSession
  ) {
    const doc = new this.pizzaOrderModel({
      ...newOrder,
      owner: userId
    });

    return doc.save({ session });
  }

  /**
   * The implementation of the `addPizzaOrderRef()` method
   * of the `UsersService` is left out for brevity here. You
   * can see that the `session` should also be used there, such
   * that the operation is added to the current session. Note
   * that sessions can be added using currying of Mongoose methods:
   *
   * @example
   *
   *   model
   *     .find()
   *     .session(session)
   */
  public addOrderRefToUser(
    userId: string, 
    orderId: string, 
    session: ClientSession
  ) {
    return this.usersService.addPizzaOrderRef(userId, orderId, session);
  }
} 
```

> 将`addOrderRefToUser`方法设为私有并从`createOrder`函数体内调用它也是非常好的。这允许`PizzaOrderResolver`只对`PizzaOrderService`方法`createOrder`进行一次调用，而没有任何额外的调用。请记住，上述实现是为了演示的目的。

# 结论

这就结束了我们对**因果一致性**的探究，以及一些用于实现的代码示例。您现在已经看到了:

*   **因果一致性的概念** *由*组成(双关语)
*   如何从一组需求中识别因果一致性的需要
*   如何在现代技术栈中实现因果一致性

感谢你阅读这篇文章。希望它能让你改进你在节点 API 上的开发，或者任何软件项目。欢迎在下面留下任何问题或评论。

您也可以在 Twitter 上关注我