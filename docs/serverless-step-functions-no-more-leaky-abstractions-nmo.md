# 无服务器阶跃函数:不再有泄漏的抽象

> 原文：<https://dev.to/theburningmonk/serverless-step-functions-no-more-leaky-abstractions-nmo>

关于[无服务器步进函数](https://github.com/horike37/serverless-step-functions)插件，我有一些激动人心的消息要和你分享。

[![](img/0fd278fc196210501fc9f9924d4dbc65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZXVvYWP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d41fd661314c.png)

使用该插件的一个主要问题是，您需要使用完整形式的 ARNs。我们在 [v1.18.0](https://github.com/horike37/serverless-step-functions/releases/tag/v1.18.0) 中通过支持 CloudFormation 固有函数`Fn:GetAtt`和`Ref`解决了这个问题。这使得您可以改为引用局部函数。

```
functions: hello-world: handler: hello-world.handlerstepFunctions: stateMachines: myStateMachine: definition: StartAt: HelloWorld States: HelloWorld: Type: Task Resource: Fn::GetAtt: [HelloDashworldLambdaFunction, Arn] End: true 
```

但是这仍然是一个有漏洞的抽象——你必须知道[无服务器框架](https://serverless.com/framework/)如何将本地函数名转换成 CloudFormation 逻辑 id。

新来的人在这里经常会感到困惑。

> “你是怎么从`hello-world`得到逻辑 ID *`HelloDashworldLambdaFunction`* 的？”

我不能责怪他们不知道。这是无服务器框架中的一个实现细节，一个你不必关心的细节！

这就是为什么我很高兴地告诉你，从 [v2.2.0](https://github.com/horike37/serverless-step-functions/releases/tag/v2.2.0) 开始，你可以在状态机定义中使用本地名字来引用本地函数。