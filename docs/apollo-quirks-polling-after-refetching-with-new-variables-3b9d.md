# Apollo Quirks:用新变量重新提取后的轮询

> 原文：<https://dev.to/petecorey/apollo-quirks-polling-after-refetching-with-new-variables-3b9d>

在为最近的一个客户项目工作时，[埃斯特尔](https://twitter.com/msestellemarie)和我遇到了*一个有趣的[阿波罗](https://www.apollographql.com/)怪癖*。事实证明，带有活动`pollInterval`的 Apollo 查询不会考虑对`refetch`的调用所提供的新变量。

为了进行演示，假设我们正在呈现一个分页的表，表中充满了从服务器获取的数据:

```
const Table = () => {
    let { data } = useQuery(gql`
        query items($page: Int!) {
            items(page: $page) {
                pages
                results {
                    _id
                    result
                }
            }
        }
    `, {
        pollInterval: 5000
    });

    return (
        <>
            <table>
                {data.items.results.map(({ _id, result }) => (
                    <tr key={_id}>
                        <td>{result}</td>
                    </tr>
                ))}
            </table>
        </>
    );
}; 
```

表中的项目会随着时间的推移而变化，所以我们每五秒钟轮询一次查询。

我们还想给用户按钮，以快速导航到给定的结果页面。例如，每当用户按下“第二页”按钮时，我们希望将我们的查询的`variables`设置为`{ page: 2 }` :

```
 const Table = () => {
- let { data } = useQuery(gql`
+ let { data, refetch } = useQuery(gql`
         query items($page: Int!) {
             items(page: $page) {
                 pages
                 results {
                     _id
                     result
                 }
             }
         }
     `, {
         pollInterval: 5000
     });

+ const onClick = page => {
+ refetch({ variables: { page } });
+ };

     return (
         <>
             <table>
                 {data.items.results.map(({ _id, result }) => (
                     <tr key={_id}>
                         <td>{result}</td>
                     </tr>
                 ))}
             </table>
+ {_.chain(data.items.pages)
+ .map(page => (
+ <Button onClick={() => onClick(page)}>
+ Page {page + 1}
+ </Button>
+ ))
+ .value()}
         </>
     );
 }; 
```

这有效…几秒钟。但是我们意外地回到了第一页。这里发生了什么事？

事实证明，我们的轮询查询将总是使用初始化轮询时给定的变量来查询服务器。因此，在我们的例子中，即使用户前进到第二页，我们的轮询查询将获取第一页并呈现这些结果。

那么我们该如何应对呢？[这个关于`apollo-client`项目](https://github.com/apollographql/apollo-client/issues/3053)的 GitHub 问题建议在改变查询变量之前调用`stopPolling`，并使用这些新变量`startPolling`重新启用轮询。

在我们的例子中，应该是这样的:

```
 const Table = () => {
- let { data, refetch } = useQuery(gql`
+ let { data, refetch, startPolling, stopPolling } = useQuery(gql`
         query items($page: Int!) {
             items(page: $page) {
                 pages
                 results {
                     _id
                     result
                 }
             }
         }
     `, {
         pollInterval: 5000
     });

     const onClick = page => {
+ stopPolling();
         refetch({ variables: { page } });
+ startPolling(5000);
     };

     return (
         <>
             <table>
                 {data.items.results.map(({ _id, result }) => (
                     <tr key={_id}>
                         <td>{result}</td>
                     </tr>
                 ))}
             </table>
             {_.chain(data.items.pages)
                 .map(page => (
                 <Button onClick={() => onClick(page)}>
                     Page {page + 1}
                 </Button>
                 ))
                 .value()}
         </>
     );
 }; 
```

而且很管用！现在，我们的轮询查询将从服务器获取正确更新的变量。当用户导航到第二页时，他们会停留在第二页！

我对为什么会发生这种情况以及为什么`stopPolling` / `startPolling`解决方案有效的最佳猜测是，当轮询开始时[,`variables`的值](https://github.com/apollographql/apollo-client/blob/2a4e4158e5d1b7249e3b6347d1038e00ec89c579/packages/apollo-client/src/core/QueryManager.ts#L1373-L1399)[被困在闭包](https://github.com/apollographql/apollo-client/blob/2a4e4158e5d1b7249e3b6347d1038e00ec89c579/packages/apollo-client/src/core/QueryManager.ts#L1349)中。当`refetch`被调用时，[改变对`options.variables`对象](https://github.com/apollographql/apollo-client/blob/master/packages/apollo-client/src/core/ObservableQuery.ts#L323-L329)、*的引用，但不改变被引用对象*。这意味着`options.variables`的值在轮询间隔内不会改变。

调用`stopPolling`和`startPolling`迫使我们的轮询间隔在新的闭包下用新的`variables`值重新开始。