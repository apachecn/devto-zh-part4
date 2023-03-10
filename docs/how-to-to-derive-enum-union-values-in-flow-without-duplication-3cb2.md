# 如何在没有重复的情况下导出流中的枚举/联合值

> 原文：<https://dev.to/aurelio/how-to-to-derive-enum-union-values-in-flow-without-duplication-3cb2>

在 Js 中处理一组预定义的字符串值时，一个常见的模式是将它们
封装在一个对象中，并将其用作一个映射。

假设我们正在处理一个实体——假设请求加入一个组——它可能处于三种且只有三种可能的状态:

1.  `approved`
2.  `rejected`
3.  `pending`

我们可以直接在代码中使用这些值:

```
if (request.status === 'approved') {
  // ...
}

// elsewhere in the code
request.status === 'approved' ? showPage() : null 
```

虽然这样做可行，但容易出错，因为我们在 IDE 中遗漏了完成部分(它只是一个字符串)，
而且它真的不是未来的证明。如果我们的后端将把 approved 的字符串改为`confirmed`或者甚至仅仅是`Approved`，我们将不得不突然修改我们的代码库来改变`approved`的所有实例(希望完全相同的字符串不要用在完全不相关的上下文中，这些上下文可能会被我们的
搜索和替换无意中改变)。

第一个补救措施通常是将这些字符串合并到一个对象中，并将其导出以用作映射。

```
export const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
}; 
```

现在我们可以重构我们的代码:

```
import { statuses } from '../../statuses.js';

if (request.status === statuses.approved) {
  // ...
}
// ...

request.status === statuses.approved ? showPage() : null 
```

在输入代码时，我们首先得到了自动完成的好处，另外我们确信
对 api 进行了轻微的修改，将字符串`approved`与`Approved`交换，我们需要修改
一行，而不是我们使用的那个值。

如果您在 Js 中使用类型检查器，那么在使用这些
状态时，您将会有更好的保证。在这种情况下，提供类型定义的自然方式是枚举。

为了在 Flow 中表达这一点，我们将使用一个联合，第一次尝试可能如下。

```
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};

type Status = 'approved' | 'rejected' | 'pending'; 
```

这绝对有效，没有任何问题。我们可以查看流程回复:

```
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};
type Status = 'approved' | 'rejected' | 'pending';

const getStatus = (obj: { status: Status }): Status => obj.status;

// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will throw an error, again, all good
const ret1 = getStatus({ status: 'nope' }); // ERROR: Cannot call `getStatus` with object literal bound to `obj` because string [1] is incompatible with enum [2] in property `status`. 
```

可以试试[这里](https://flow.org/try/#0PQKgBAAgZgNg9gdzCYAoVBjOA7AzgFzAIEN8BXXAU1zAF4wBvVMMYgBzYCc4A3SgEwBcYAOTsuvASIA0zMJ0oArShnwDhIhctVTZLNpWz8AltgDmGg0dNmRqAL4BudPgCeBsAGV8pCnVHi3Hz8ImAAPqJaKmoh4aJWJuYizpg4BGBmlPjevjT0ABRwAEaKwgxEPuS4wjlVYPYAlDWVfrQAfGDFigB0JFUpWHiECvgADP6Z2S24+eV9FMLzVLjdgZL8jc7AwGAAJACiAB4Gqvuc3JypQ-JZAIwTWbUUsxW5GthwBqGbYKjbYPsAEqAgDygOEAGFiNgPoQMMQYDAwAADSZPXDIsAIYz4AAWnRK0TAMBxlE4CLARTgZCMYHwcBRXUxRRUxAolAqnBsYAA2rcALpgYw0UxYAC2bFIxiKMA52LxYEMZDFvIATILTGAJAZOG4UUtkd1UEA)。

虽然这样做了，但我们必须注意重复，因为我们手动将对象的所有值
转录为`Status`类型。这可能还可以，因为我们只使用了 3 个值，但是如果值必须增加到 10 呢？还是到 20？
保持类型定义最新将变得乏味且容易出错。

解决方法是使用`$Values`和`typeof`提取值:

```
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};
type Status = $Values<typeof statuses>; 
```

如果我们尝试这样做，我们会看到一个奇怪的行为:

```
// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will pass!! That's not good!
const ret1 = getStatus({ status: 'nope' }); 
```

可以试试[这里](https://flow.org/try/#0PQKgBAAgZgNg9gdzCYAoVBjOA7AzgFzAIEN8BXXAU1zAF4wBvVMMYgBzYCc4A3SgEwBcYAOTsuvASIA0zMJ0oArShnwDhIhctVTZLNpWz8AltgDmGg0dNmRqAL4BudPgCeBsAGV8pCnTAAJABqxDBk1AA8bgZwUEQ+5FS4AHzOmDgEYGaU+N6+NPQAFHAARorCDPH5wnmJYPYAlDUJfrTJYKWKAHQkiWnAwGD4ABbGNAjGMDBgbMS4uNKsU1lwcPzpeIQK+AAM-tm5LbiFlb0UwmdJXeLcfPyN-YMjY2ATy7PzAISfYAAqw6QRDRsHBCGZVvxPhtMtsAIz7HK1CgnKqJDQggwieoNRxgIA)。

如果我们问 IDE 我们的`getStatus`函数的返回类型，我们会看到它实际上是一个`string`而不是
我们的`Status`联合类型的成员之一。

[![code editor tells us that it's only expecting a string](img/ce34c2cb16e42368eabfec93a30dc3bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vfyz24cu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6a3fnarexj1bkeddgc10.png)

原因在于，当我们在 Flow 中创建一个具有属性的对象时，它被视为一个*密封对象*。来自文档(重点是我的):

> 当您创建一个包含其属性的对象时，您在 Flow 中创建了一个密封的对象类型。这些密封的对象将知道你声明它们的所有属性以及它们的值的类型。

另外:

> 但是当对象被密封时，Flow 不允许你向它们添加新的属性。

首先让我们弄清楚什么是密封物体。从 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/seal) :

> Object.seal()方法密封一个对象，防止向其添加新属性，并将所有现有属性标记为不可配置。只要当前属性的值是可写的，它们仍然可以被改变。

这意味着我们将不能向这个对象添加新的键(它将抛出一个错误)，但是我们将能够改变现有的值。在这种情况下，Flow 将只能推断出值的类型，而不能推断出它们的文字值，因为它们仍然可以在以后的代码中被修改。

让 Flow 真正将这些文字值提取到一个枚举中的技巧是像这样冻结对象:

```
const statuses = Object.freeze({
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
}); 
```

`Object.freeze`要做的是做`Object.seal`所做的一切，并确保没有键/值对可以被修改。

再次提醒 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) :

> 方法的作用是:冻结一个对象。冻结的对象不能再被改变；冻结对象可防止向其添加新属性、删除现有属性、更改现有属性的可枚举性、可配置性或可写性，以及更改现有属性的值。此外，冻结对象还可以防止其原型被更改。freeze()返回传入的同一个对象。

最后，现在 Flow 将能够正确地提取值(而不仅仅是它们的原始类型)。来测试一下:

```
const statuses = Object.freeze({
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
});

type Status = $Values<typeof statuses>;

const getStatus = (obj: { status: Status }): Status => obj.status;

// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will throw an error again, exactly as we want
const ret1 = getStatus({ status: 'nope' });  //  ^ Cannot call `getStatus` with object literal bound to `obj` because string [1] is incompatible with enum [2] in property `status`. 
```

我们现在确信我们可以扩展`statuses`来支持更多的键/值，而不会复制我们的联合类型中的值。

## 参考文献

*   [对象类型推理](https://flow.org/en/docs/types/objects/#toc-object-type-inference)上的流转单据
*   对[object . freeze vs object . seal](https://stackoverflow.com/a/21402679/1446845)的另一个清晰而简短的解释
*   这张令人惊叹的封面图片是由吉安-烈斗·塔努策拍摄的

*原载于[我的博客](https://nobitagit.github.io/blog/How-to-derive-union-values-flow-type/)T3。*