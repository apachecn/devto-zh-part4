# 类型脚本泛型:一个基本用例

> 原文：<https://dev.to/stephencweiss/typescript-generics-a-basic-use-case-5dkh>

什么时候在 Typescript 类型中使用泛型有意义？如何创建接受泛型的类型？

我已经通读了几次关于泛型的 Typescript 文档。每一次，我都会收集到更多的信息，但是在我有机会在实践中看到它之前，我从未真正喜欢上使用泛型，这要感谢一位同事的指导。

下面是我学到的一些经验教训的总结，以及它是如何帮助事情变得有条不紊的。

# 上下文

问题始于试图扩展一个 API 来返回一些额外的信息。在这种情况下，我希望包含我们称之为可审计的字段，其中包含修改的时间戳和修改的用户。

这些字段已经是应用程序中更大的可审计界面的一部分:

```
export interface Auditable {
  // […]
  _modifiedAt: Date;
  _modifiedBy: string;
  // […]
} 
```

查询的目的是返回对几个不同的表所做的更改- `resources`、`fields`等。

数据的大致形状如下:

```
export interface ChangeLog {
  _startversion?: number;
  _endversion?: number;
  resources?: {
    Property: StartEndBox<…> 
  };
  fields?: {
    [resource_name: string]: {
      [standard_name: string]: StartEndBox<…> 
    };
  };
  // […]
} 
```

这里的重点不是`StartEndBox`(这是一个接口，用于选择我们正在比较差异的字段)，而是每个不同的表最终都可能有不同的结构。

幸运的是，无论它在哪个级别——我知道一旦我到达那里，那就是我的可审计字段所在的位置，我可以在那里附加我的数据——所以，对于接口，那就是我添加它的位置。例如:

```
export interface ChangeLog {
  _startversion?: number;
  _endversion?: number;
  resources?: {
    Property: StartEnd<…> &
      Pick<metadata.resources.Resource, "_modifiedAt" | "_modifiedBy">;
  };
  fields?: {
    [resource_name: string]: {
      [standard_name: string]: StartEndBox<…> &
       Pick<metadata.fields.Field, "_modifiedAt" | "_modifiedBy">;
    };
  };
  // […]
} 
```

注意，在这个实例中，我只从模型中选择了我想要的字段，但是我为每个实例复制了文本。

如果将来我想添加一个字段，我将不得不更新每个 Pick 语句——这增加了我错过或遗漏它的可能性。

# 重构到一个通用的方法

泛型是支持可重用组件的类型脚本。因为*输入*可以改变，所以类型需要能够适应它。如果类型与组件耦合得太紧，那么它只对那个实例有效。

回头看看上面的例子，我能够从`Resource`和`Field`中选择相同的可审计字段的*原因*是因为这两个模型*已经扩展了* `Auditable`。

有了这些知识，我们就可以利用它来创造通用产品。

```
export type AuditableFields<T extends Auditable> = Pick<T, “_modifiedAt” | “_modifiedBy”> 
```

如上定义一个`AuditableFields`类型，我现在可以在`ChangeLog` :
中引用它

```
export interface ChangeLog {
  _startversion?: number;
  _endversion?: number;
  resources?: {
    Property: StartEnd<…> &
      AuditableFields<metadata.resources.Resource>;
  };
  fields?: {
    [resource_name: string]: {
      [standard_name: string]: StartEndBox<…> &
       AuditableFields<metadata.fields.Field>;
    };
  };
  // […]
} 
```

现在，我不再重复自己，而是在上下文中指定我想要的类型。像这样使用泛型使代码更具声明性。我不是凭直觉从模型中挑选某些字段，而是声明我想要来自 X、Y 或 Z(或者可以说是 T)的可审计字段。

# 总结

让我们回到开始的两个问题。我们知道如何回答它们了吗？

1.  什么时候在 Typescript 类型中使用泛型有意义？如果你发现自己一遍又一遍地输入同一个接口，但只交换了它的引用——这是一个好的迹象，表明你可以从泛型中获益。
2.  如何创建接受泛型的类型？像变量定义一样，定义泛型类型意味着以某种抽象逻辑的方式将各个部分分开。但是，这样做，您就有了一个可以共享的集中配置点，减少了重复和出错的机会。

# 脚注

*   <sup>1</sup> [泛型|打字稿](https://www.typescriptlang.org/docs/handbook/generics.html)