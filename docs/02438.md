# AWS 用类型脚本和钩子放大 GraphQL 操作——第 4 部分[订阅]

> 原文：<https://dev.to/mwarger/aws-amplify-graphql-operations-with-typescript-and-hooks-part-4-subscriptions-h0j>

接下来是订阅。这是 GraphQL 和 AppSync 的一个有趣的特性。利用实时数据的能力确实可以为你的应用带来一些不错的 UX。我倾向于保守地使用它，但是它对于小列表和即时反馈非常有帮助。

我们以前的帖子已经建立了一套相当不错的抽象，我们也可以在这里使用。正因为如此，这次我要把结果贴在开头，把它拆成碎片。喝杯咖啡，我们就开始吧。

## 代码

这是我们完成的定制钩子:

```
type ConfigType<VariableType extends {}> = {
  query: string;
  key: string;
  variables?: VariableType;
};

export const useSubscriptionByItself = <
  ItemType extends { id: string },
  VariablesType extends {} = {}
>({
  config,
  itemData,
}: {
  config?: ConfigType<VariablesType>;
  itemData?: ItemType;
} = {}) => {
  const [item, update] = React.useState<ItemType | undefined>(itemData);

  React.useEffect(() => {
    let unsubscribe;
    if (config) {
      const { query, key, variables } = config;
      const subscription = API.graphql(graphqlOperation(query, variables));
      if (subscription instanceof Observable) {
        const sub = subscription.subscribe({
          next: payload => {
            try {
              const {
                value: {
                  data: { [key]: item },
                },
              }: {
                value: { data: { [key: string]: ItemType } };
              } = payload;

              update(item);
            } catch (error) {
              console.error(
                `${error.message} - Check the key property: the current value is ${key}`
              );
            }
          },
        });
        unsubscribe = () => {
          sub.unsubscribe();
        };
      }
    }
    return unsubscribe;
  }, [JSON.stringify(config)]);

  return [item];
}; 
```

这里有很多，但我们的用例很简单。我们的订阅将处理一个项目。这可能很简单，就像订阅新创建的博客文章一样，例如:

```
const [item] = useSubscription<postFragment>({
  config: {
    key: 'onCreatePost',
    query: onCreatePost,
  },
}); 
```

我们还可以传递一些变量来订阅更新后的评论:

```
const [comment] = useSubscriptionByItself<
  commentFragment,
  onUpdateCommentSubscriptionVariables
>({
  itemData: comment,
  config: {
    key: 'onUpdateComment',
    query: onUpdateComment,
    variables: {
      id,
    },
  },
}); 
```

> 重点是，我们能够提取`const subscription = API.graphql(graphqlOperation(query, variables));`的样板文件，并将其提取到可以重用的内容中，以及学习 AWS Amplify 如何返回数据的惯例，以强类型的方式处理一切。

让我们从顶部开始，看看发生了什么。

## 输入配置

```
type ConfigType<VariableType extends {}> = {
  query: string;
  key: string;
  variables?: VariableType;
};

export const useSubscription = <
  ItemType extends { id: string },
  VariablesType extends {} = {}
>({
  config,
  itemData,
}: {
  config?: ConfigType<VariablesType>;
  itemData?: ItemType;
} = {}) => { 
```

让我们看看类型参数(尖括号之间的东西)。这需要一些解释，因为我开始假设一个约定。`ItemType`表示我们将在钩子中返回并操作的对象。`extends { id: string }`意味着无论我们传入什么对象，它都必须有一个类型为`string`的 id 作为属性。这很有用，因为我们需要对象的唯一标识符。在我们想要初始化我们的状态时使用的`itemData`。

请注意，我利用片段来提供我们可以使用的单一类型对象。一旦创建，Amplify `codegen`工具将为你的片段创建类型，然后你可以像我们在这个例子中一样使用它们。你可以在这里了解更多关于片段以及如何在 GraphQL 中使用它们。

第二个`VariableType`将是一个对象，表示我们将传递给订阅`graphqlOperation`的任何变量。这在`ConfigType`的类型声明中被进一步使用。这代表保存订阅的配置`query`、`variables`和`key`，我们将使用它们来建立我们的订阅。稍后，我们将回到`key`。

## 状态

```
const [item, update] = React.useState<ItemType | undefined>(itemData); 
```

这很简单。我们使用传入的`ItemType`参数来键入`useState`函数。这可能是未定义的，所以我们也注意到了。如果我们传入了初始的`itemData`，我们也用它来建立状态，以跟踪我们正在处理的订阅。

## 大意

这是真正的精华。

```
React.useEffect(() => {
  let unsubscribe;
  if (config) {
    const { query, key, variables } = config;
    const subscription = API.graphql(graphqlOperation(query, variables));
    if (subscription instanceof Observable) {
      const sub = subscription.subscribe({
        next: payload => {
          try {
            const {
              value: {
                data: { [key]: item },
              },
            }: {
              value: { data: { [key: string]: ItemType } };
            } = payload;

            update(item);
          } catch (error) {
            console.error(
              `${error.message} - Check the key property: the current value is ${key}`
            );
          }
        },
      });
      unsubscribe = () => {
        sub.unsubscribe();
      };
    }
  }
  return unsubscribe;
}, [JSON.stringify(config)]); 
```

首先，我们将建立一个订阅，所以为了有效，我们需要在完成后[清理它](https://reactjs.org/docs/hooks-effect.html#example-using-hooks-1)。我们声明一个变量，当从效果返回时，它将保存我们想要运行的函数。

接下来，我们将检查配置是否存在，因为它是可选的。我们析构这些组件，并用它们来构建我们的订阅。接下来的几行很重要:

```
 const subscription = API.graphql(graphqlOperation(query, variables));
  if (subscription instanceof Observable) {
    ... 
```

`API.graphql`调用实际上返回了`Observable | Promise<>`——这意味着结果将是一个或另一个。为了获得我们期望的自动完成帮助(并阻止 TypeScript 对我们大喊大叫),我们需要使用[类型保护](https://basarat.gitbooks.io/typescript/docs/types/typeGuard.html)进行所谓的“类型收缩”。我们通过使用`instanceof`关键字来检查类型是否是一个`Observable`来做到这一点。我添加了`@types/zen-observable`包(`yarn add -D @types/zen-observable`)来提供类型。

## 订阅

```
const sub = subscription.subscribe({
  next: payload => {
    try {
      const {
        value: {
          data: { [key]: item },
        },
      }: {
        value: { data: { [key: string]: ItemType } };
      } = payload;

      update(item);
    } catch (error) {
      console.error(
        `${error.message} - Check the key property: the current value is ${key}`
      );
    }
  },
});
unsubscribe = () => {
  sub.unsubscribe();
}; 
```

我们有从 graphql 调用返回的订阅，所以现在我们需要订阅它。这就是所谓的可观察。据我所知，Amplify 正在使用 [zen-observable](https://github.com/zenparsing/zen-observable) 库来实现订阅。Observables 通过将值作为流返回来操作它们，因此您可以通过提供回调来监听流的更新——在本例中是`next`。我们的`next`回调接受一个`payload`(这将是流中下一个事件的值)，然后我们对这个值做一些析构来获得我们想要的底层数据。Amplify 遵循在订阅中返回数据的约定，所以我们可以用它来确保我们的析构是正确的。

```
const {
  value: {
    data: { [key]: item },
  },
}: {
  value: { data: { [key: string]: ItemType } };
} = payload; 
```

我们使用前面谈到的`key`以及传入的`ItemType`类型来创建一个类型，并适当地从嵌套对象中析构(以`value.data[key]`的形式)。一旦处理了这些数据，我们就使用来自我们的`useState`钩子的`update`方法来保存我们的状态，如果有任何出错，我们就记录错误。

回调之后，我们将一个小箭头函数赋给我们的`unsubscribe`变量，如果使用钩子的组件被卸载，这个函数将完成取消订阅的工作。

```
[JSON.stringify(config)]; 
```

我们的`useEffect`钩子接受一个依赖项(一个对象),所以我们只需`stringify`它，以确保如果它以任何方式改变，我们的钩子将再次运行，我们可以重新建立适当的订阅。

最后一行仅仅返回保存在 state 中的数据，所以我们可以从调用者那里使用它。

```
return [item]; 
```

## 裹

就其核心而言，这只是现有 Amplify 工具的一个包装。但是对于 TypeScript 项目，它为您提供了帮助，您可以使用它来确保您的应用程序正在做您期望的事情。在我看来，好的副产品是 API 表面更加完整，同时去掉了公共部分。通常一个好的做法是将这些东西提取出来，避免在组件中直接使用`useEffect`。这只是朝着这个方向迈出的一小步。

如果有人对这种方法有任何反馈，请在评论中留下。我经常用这个，只在 TypeScript 项目中用，希望能帮到某个人。也可以在 twitter 上 ping 我[@ m larger](https://dev.to/mwarger)。

...

但是等等，如果你需要订阅很多活动呢？这是下一个——关注我，当它出版时会通知我！