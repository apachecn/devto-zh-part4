# Nestjs &控制反转(IoC)

> 原文：<https://dev.to/sergey_telpuk/nestjs-inversion-of-control-ioc-md2>

朋友们好！
我想谈谈 [Nestjs](https://nestjs.com) 和 [IoC](https://en.wikipedia.org/wiki/Inversion_of_control) 。首先，我想指出我不是 JavaScript 世界的专家，但是我在寻找适合我的 js 项目的最佳编码实践。
Nestjs 有很棒的文档。我熟悉了它，并看到了使用它的许多好处。安装骨架很容易。我不想浪费时间去展示它。让我们开始描述如何使用 IoC。
创建一个人工界面:

```
interface IContrived {
    say():void;
} 
```

创建`ContrivedService`并实现`IContrived`接口:

```
class ContrivedService implements IContrived{
    say():void{}
} 
```

然后为该接口添加服务提供商:

```
const ContrivedServiceProvider: Provider = {
    provide: 'IContrived',//it's an injectable interface 
    useClass: ContrivedService,
}; 
```

将`ContrivedServiceProvider`加入`ContrivedModule` :

```
@Module({
    controllers: [],
    providers: [
        ContrivedServiceProvider,
    ],
    imports: [],
})
export class ContrivedModule {} 
```

借助接口使用 IoC 就足够了。最后一步是注入我们的接口。
将`ContrivedService`注入`ContrivedController`例如:

```
export class ContrivedController {
    constructor(
        @Inject('IContrived')
        private readonly contrivedService: IContrived
    ) {} 
```

这是 IoC 的一个简单实现，也是实现完全松耦合设计的第一步。如果你有任何问题，请随时联系我。

最诚挚的问候！