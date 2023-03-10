# AWS 用类型脚本和钩子放大 GraphQL 操作——第 3 部分[突变]

> 原文：<https://dev.to/mwarger/aws-amplify-graphql-operations-with-typescript-and-hooks-part-3-mutations-1f66>

让我们快速看一下突变。这将是一个简短的帖子，因为 Amplify 使突变相对无痛，我们将看到的 GraphQL 突变的格式非常类似于我们已经看到的查询(查看本系列中以前的帖子以了解更多信息)。

## 突变

为了设置突变，我们需要查看我们在上一节中所做的查询的一部分。

```
const fetchQuery = async (query: string, variables?: VariablesType) => {
  try {
    setLoading(true);
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
```

所有 GraphQL API 调用都是通过 Amplify 使用`graphqlOperation`函数进行的。我们可以重用它，所以让我们创建另一个函数来处理数据的返回。

```
export const gqlOp = async <
  ResultType extends {},
  VariablesType extends {} = {}
>(
  query: string,
  variables?: VariablesType
) => {
  const { data } = (await API.graphql(graphqlOperation(query, variables))) as {
    data: ResultType;
  };
  return data;
}; 
```

现在我们可以用这个小函数代替 fetch 调用。

```
const fetchQuery = async (query: string, variables?: VariablesType) => {
  try {
    setLoading(true);
    const data = await gqlOp<ResultType, VariablesType>(query, variables)
    setData(data);
  } catch (error) {
    console.log(error);
    setError(error);
  } finally {
    setLoading(false);
  }
}; 
```

现在已经完成了，让我们为突变创建一个函数。

```
export const mutation = async <
  ResultType extends {},
  VariablesType extends {} = {}
>(
  query: string,
  variables?: VariablesType
) => gqlOp<ResultType, VariablesType>(query, variables); 
```

好吧，就这样。感谢阅读！

请继续关注下一个我们解决订阅问题的论坛。