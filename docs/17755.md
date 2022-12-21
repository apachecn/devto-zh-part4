# AWS 用类型脚本和钩子放大 GraphQL 操作——第 2 部分[定制钩子]

> 原文：<https://dev.to/mwarger/aws-amplify-graphql-queries-with-typescript-and-hooks-part-2-custom-hooks-57ho>

定制钩子允许你提取常用的功能，用于你的应用程序的多个部分。在上一篇文章中，我们讨论了如何利用 hooks 和 Amplify library with TypeScript 来获取一些数据。这涉及到一些常见的元素，所以让我们通过提取一个自定义钩子来使我们的数据获取更加有用，我们可以在任何时候查询我们的 API。

## 钩子:

这是我们在上一篇文章中留下的代码:

```
const [gameData, setGameData] = React.useState(undefined); 

React.useEffect(() => {
  const fetchGames = async () => {
    try {
      const response: any = await API.graphql(graphqlOperation(popularGames)); 
      setGameData(response.data); 
    } catch (error) {
      console.log(error);
    }
  };

  fetchGames();
}, []); 
```

让我们把它放入一个名为`useQuery`的定制钩子中。我们需要重命名我们的`useState`调用的返回值，因为我们不是在专门讨论游戏内容，我们是在做一个可重用的钩子。

结果如下:

```
const useQuery = () => {
  const [data, setData] = React.useState(undefined);

  React.useEffect(() => {
    const fetchQuery = async () => {
      try {
        const response: any = await API.graphql(graphqlOperation(popularGames));
        setData(response.data); 
      } catch (error) {
        console.log(error);
      }
    };

    fetchQuery();
  }, []);
}; 
```

等等，那看起来什么都不像。它只是一个函数。

完全正确。

钩子是一些函数，我们可以用它们来封装和组合我们想在代码中利用的其他钩子。

## 传递参数

有些东西我们需要归纳，以便能够在其他地方重用这个函数。也就是说，`popularGames`查询和任何可能的变量(这个例子没有任何变量，但是我们希望包含其他可能的查询)。

我们可以通过向钩子传递参数来实现这一点，就像任何其他函数一样。

```
const useQuery = (query: string, variables?: any) => {

  const [data, setData] = React.useState(undefined);

  React.useEffect(() => {
    const fetchQuery = async () => {
      try {
        const response: any = await API.graphql(graphqlOperation(query)); 
        setData(response.data);
      } catch (error) {
        console.log(error);
      }
    };

    fetchQuery();
  }, [query, variables]); 
}; 
```

我们在这里做了一些事情，包括添加两个新参数(`query`和`variables`)，将它们添加到`useEffect`声明的依赖数组中，并更新`graphqlOperation`以获取`query`。

然而，在这样做的过程中，我们失去了在上一篇文章的例子中使用的类型提供给我们的任何帮助。我们需要解决这个问题，但是怎么解决呢？

## 仿制药

泛型允许我们将类型传递给函数，就像将参数传递给函数一样。在这种情况下，我们希望将泛型类型参数传递给我们的`useQuery`钩子，以给它关于我们的`query`和`variables`是什么类型的信息。

看起来是这样的:

```
 const useQuery = <ResultType extends {}, VariablesType extends {} = {}>(

  query: string,
  variables?: VariablesType 
) => {
  const [data, setData] = React.useState({} as ResultType);

  React.useEffect(() => {
    const fetchQuery = async () => {
      try {
        const { data } = (await API.graphql(
          graphqlOperation(query, variables)
        )) as {
          data: ResultType;
        };

        setData(data);
      } catch (error) {
        console.log(error);
      }
    };

    fetchQuery();
  }, [query]);
}; 
```

这里，我们说我们可以传递两种类型给我们的`useQuery`钩子。第一种类型是我们将从网络调用中返回的`ResultsType`。第二个是`VariablesType`,如果我们想要将变量传递给我们的 Amplify 查询调用，我们可以使用它。我们通过在声明后放置`= {}`将它默认为一个空对象。我们在函数的可选的`variables`声明之后使用这个类型。

## 返回数据

你可能已经注意到了，我们没有从这个钩子返回任何东西。我们正在获取数据，这些数据被设置为钩子内的状态——但仅此而已。我们可以从这个钩子返回我们想要的任何东西，比如我们的数据，我们还可以返回有用的信息，比如我们是否正在加载数据或者遇到了错误。我们可以添加一些状态来跟踪这些状态，并返回一个包含不同状态的对象。看起来是这样的:

```
type UseQueryType<ResultType> = {
  loading: boolean;
  error: any;
  data: ResultType;
  refetch: () => void;
};

export const useQuery = <ResultType extends {}, VariablesType extends {} = {}>(
  query: string,
  variables?: VariablesType
): UseQueryType<ResultType> => {
  const [loading, setLoading] = React.useState(true); 
  const [error, setError] = React.useState(''); 
  const [data, setData] = React.useState({} as ResultType);

  const fetchQuery = async (query: string, variables?: VariablesType) => {
    try {
      const { data } = (await API.graphql(
        graphqlOperation(query, variables)
      )) as {
        data: ResultType;
      };
      setData(data);
    } catch (error) {
      console.log(error);
      setError(error);
    } finally {
      setLoading(false);
    }
  };

  React.useEffect(() => {
    fetchQuery(query, variables);
  }, [query]);

  return {
    loading,
    data,
    error,
  };

}; 
```

我们要将加载状态初始化为 true，将错误设置为空字符串，然后将对象中所有有状态的部分返回给客户端。我们要做的最后一件事是设置函数的返回类型，我们可以再次利用泛型来创建封装状态不同部分的另一种类型。

该类型将如下所示:

```
type UseQueryType<ResultType> = {
  loading: boolean;
  error: any;
  data: ResultType;
}; 
```

我们可以通过在钩子后声明返回类型来使用它，就像这样:

```
export const useQuery = <ResultType extends {}, VariablesType extends {} = {}>(
  query: string,
  variables?: VariablesType
): UseQueryType<ResultType> => { 
  ... 
```

## 添加方便

为了方便起见，我们将为我们的钩子添加一个函数，我们可以将它传递回钩子的消费者，让我们重新获取数据(如果我们愿意，也许可以手动刷新列表)。我在这里添加了这个能力:

```
type UseQueryType<ResultType> = {
  loading: boolean;
  error: any;
  data: ResultType;
  refetch: () => void;
};

export const useQuery = <ResultType extends {}, VariablesType extends {} = {}>(
  query: string,
  variables?: VariablesType
): UseQueryType<ResultType> => {
  const [loading, setLoading] = React.useState(true);
  const [error, setError] = React.useState('');
  const [data, setData] = React.useState({} as ResultType);

  const fetchQuery = async (query: string, variables?: VariablesType) => {
    try {
      const { data } = (await API.graphql(
        graphqlOperation(query, variables)
      )) as {
        data: ResultType;
      };
      setData(data);
    } catch (error) {
      console.log(error);
      setError(error);
    } finally {
      setLoading(false);
    }
  };

  const refetch = () => {
    fetchQuery(query, variables);
  };

  React.useEffect(() => {
    fetchQuery(query, variables);
  }, [query]);

  return {
    loading,
    data,
    error,
    refetch,
  };
}; 
```

很好，mmkay——这是一个非常有趣的钩子，可以从我们的托盘中取出很多样板文件。你可以在应用程序中尝试一下，它会很好地获取数据。但是，如果你在 create-react-app 的最新版本中运行它，你会看到一个关于钩子依赖的警告。问题是对于我们的`useEffect`钩子，我们提供了`query`参数，但是没有提供我们在钩子中使用的`variables`。这意味着，如果变量改变，效果将不会重新运行，我们将会错误地运行带有过时变量的`graphqlOperation`。

我们可以通过向依赖数组添加变量来解决这个问题。

```
React.useEffect(() => {
  fetchQuery(query, variables);
}, [query, variables]); 
```

但是如果你运行这个，你将会以钩子无限循环结束！这是为什么呢？`variables`是一个对象，对于作为对象的依赖项，我们需要比较它们的属性，React 默认情况下不会这样做。我们可以自己编写这种能力。但幸运的是，[肯特·多兹已经有了](https://github.com/kentcdodds/use-deep-compare-effect)。

通过运行`npm i use-deep-compare-effect`(或`yarn add use-deep-compare-effect`)来安装它。

接下来，我们需要更新我们的`useEffect`调用来使用它。

首先，导入它:

```
import useDeepCompareEffect from 'use-deep-compare-effect'; 
```

并将调用替换为`useEffect` :

```
useDeepCompareEffect(() => {
  fetchQuery(query, variables);
}, [query, variables]); 
```

现在，您的对象将使用深度相等进行比较，无限循环将成为过去。

## 包装完毕

下面是这个特定演示的最终版本。我希望这有助于解释在使用 Amplify、GraphQL 和 TypeScript 时钩子的一些不同方面。在 twitter 上关注我，让我知道你是否觉得它有帮助或者你是否有任何问题！

```
type UseQueryType<ResultType> = {
  loading: boolean;
  error: any;
  data: ResultType;
  refetch: () => void;
};

export const useQuery = <ResultType extends {}, VariablesType extends {} = {}>(
  query: string,
  variables?: VariablesType
): UseQueryType<ResultType> => {
  const [loading, setLoading] = React.useState(true);
  const [error, setError] = React.useState('');
  const [data, setData] = React.useState({} as ResultType);

  const fetchQuery = async (query: string, variables?: VariablesType) => {
    try {
      const { data } = (await API.graphql(
        graphqlOperation(query, variables)
      )) as {
        data: ResultType;
      };
      setData(data);
    } catch (error) {
      console.log(error);
      setError(error);
    } finally {
      setLoading(false);
    }
  };

  const refetch = () => {
    fetchQuery(query, variables);
  };

  useDeepCompareEffect(() => {
    fetchQuery(query, variables);
  }, [query, variables]);

  return {
    loading,
    data,
    error,
    refetch,
  };
}; 
```

> 这个帖子最初发布在[我的网站](https://mw.codes/posts/amplify-ts-query-custom-hook)上。你可以看到这篇文章和其他类似的文章，以及我的一切。请在 [@twitter](https://twitter.com/mwarger) 上关注我，如果您有任何问题，请联系我！