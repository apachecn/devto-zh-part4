# 用 Flutter 探索 GraphQL

> 原文：<https://dev.to/solutelabs/exploring-graphql-with-flutter-4mdp>

在这篇博文中，让我们讨论一下用 Flutter 实现 GraphQL 的各种方法

## 首先，这个 GraphQL 是什么？

在这篇文章中，我们不会深入讨论 GraphQL 的概念，但这里有一个来自官方文档的简短描述

GraphQL 是一种用于 API 的查询语言，也是一种用现有数据完成这些查询的运行时语言。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

GraphQL 是由脸书的技术团队开发的，现在是开源的，这是一种在应用程序中实现 API 的不同方式。*让我们看看如何用 Flutter 实现这个*

## 我将如何在 Flutter 上处理这个问题？

虽然您可以使用普通的 HTTP GET 请求来执行图形查询，但 Flutter 有一些很酷的 pubs 来管理这些东西，并使开发人员的工作变得容易。

如果你寻找 GraphQL client for Flutter，有很好的选择，比如 [graphql_flutter](https://pub.dev/packages/graphql_flutter#-readme-tab-) 。

graphql_flutter pub 提供了图形查询的小部件级实现。
例如:

```
Query(
  options: QueryOptions(
    document: readRepositories, // this is a Graph Query
    variables: {
      'nRepositories': 50,
    },
    pollInterval: 10,
  ),
  builder: (QueryResult result, { VoidCallback refetch }) {
    if (result.errors != null) {
      return Text(result.errors.toString());
    }

    if (result.loading) {
      return Text('Loading');
    }

    // it can be either Map or List
    List repositories = result.data['viewer']['repositories']['nodes'];

    return ListView.builder(
      itemCount: repositories.length,
      itemBuilder: (context, index) {
        final repository = repositories[index];

        return Text(repository['name']);
    });
  },
); 
```

这是一个非常高级的小部件实现，它将从小部件本身管理网络。

## 但是，我将如何在我的 App 中分离网络层？🤷

是啊！，这也是我们质疑的。因为我们在一个 Flutter 应用程序中使用了 BLoC 架构，所以这种实现肯定不适合 suits。

为了回答这个问题，我们分析了这个 pub 的内部实现。这是结果。

## 以阻塞方式实现 GraphQL

这里有一个不错的酒馆 [graphql_client](https://pub.dev/packages/graphql) 。这是被 [graphql_flutter](https://pub.dev/packages/graphql_flutter#-readme-tab-) 使用的同一家酒馆。这样，我们应该能够在 App 中分离一个网络层

让我们以 [Github Graph API](https://developer.github.com/v4/) 为例

让我们直接进入代码

*   步骤 1:基本设置

```
//Provide a Base URL
final HttpLink _httpLink = HttpLink(
  uri: 'https://api.github.com/graphql',
);

//Provide authentication, this will go as a Header in Request
final AuthLink _authLink = AuthLink(
  getToken: () async => 'Bearer $GITHUB_TOKEN',
);

final Link _link = _authLink.concat(_httpLink as Link);

GraphQLClient _client;

// Create a common client for further requests
GraphQLClient getGraphQLClient() {
  _client ??= GraphQLClient(
    link: _link,
    cache: InMemoryCache(storageProvider: () {
      return getTemporaryDirectory(); // Provide a cache mechanism
    }),
  );

  return _client;
} 
```

*   步骤 2:存储库层

```
class GitHubRepoProvider {
// method will give us Data model
  Future<List<GithubRepo>> getCurrentUserRepos() {
    return getGraphQLClient().query(_queryOptions()).then(_toGitHubRepo);
  }

// provides Graph Query options, we can provide the optional variable here
  QueryOptions _queryOptions() {
    return QueryOptions(
      document: readRepositories,
      variables: <String, dynamic>{
        'nRepositories': 50,
      },
    );
  }

// parse JSON to Data model
  List<GithubRepo> _toGitHubRepo(QueryResult queryResult) {
    if (queryResult.hasErrors) {
      throw Exception();
    }

    final list =
        queryResult.data['viewer']['repositories']['nodes'] as List<dynamic>;

    return list
        .map((repoJson) => GithubRepo.fromJson(repoJson))
        .toList(growable: false);
  }
}

// Graph Query to get repository of current user
const String _readRepositories = r'''
  query ReadRepositories($nRepositories: Int!) {
    viewer {
      repositories(last: $nRepositories) {
        nodes {
          name
          createdAt
          forkCount
        }
      }
    }
  }
'''; 
```

宾果游戏👍。我们现在有了一个数据模型。将您的数据模型转换成视图模型😉

您可以在这里看到完整的工作演示应用程序👇。(不要忘记编辑`config.dart`文件来添加您的 Github API 令牌)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)t1【milnd-mevada-STL】/[颤振图 QL 演示](https://github.com/milind-mevada-stl/Flutter-GraphQL-Demo)

### 这个项目演示了 GraphQL 在 Flutter 中的实现

<article class="markdown-body entry-content container-lg" itemprop="text">

# 浮动图形 SQL 演示 [![Build Status](img/8d0674324439e4a72600f106d049221e.png)](https://travis-ci.org/milind-mevada-stl/Flutter-GraphQL-Demo)

这个应用程序展示了使用 [GraphQL 客户端](https://pub.dev/packages/graphql)在 Flutter 中实现 GraphQL 的演示。点击这里，查看我们的博客文章[。](https://dev.to/solutelabs/exploring-graphql-with-flutter-4mdp)

## 入门指南

克隆并编辑`config.dart`文件，添加您的个性化 Github API 令牌即可开始。

</article>

[View on GitHub](https://github.com/milind-mevada-stl/Flutter-GraphQL-Demo)

在评论区提供你的想法/建议，或者给我发推文👉

> ![Milind Mevada profile image](img/e11904cfc57b8b444e0265c0947bd31d.png)米林德美瓦达[@米林德美瓦达](https://dev.to/milindmevada)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)探索 [#GraphQL](https://twitter.com/hashtag/GraphQL) 上[#颤振](https://twitter.com/hashtag/Flutter)。使用 GraphQL 客户端发布。下面是一个示例演示应用程序[github.com/milind-mevada-…](https://t.co/Hok8XJw51w)
> 
> 感谢 [@SmitSonani](https://twitter.com/SmitSonani) 的快速代码审查。
> 
> [@ FlutterDev](https://twitter.com/FlutterDev)[@ flutterhm](https://twitter.com/FlutterAHM)[@ FlutterComm](https://twitter.com/FlutterComm)[@ r _ FlutterDev](https://twitter.com/r_FlutterDev)[@ FlutterWk](https://twitter.com/FlutterWk)[@ dart _ lang](https://twitter.com/dart_lang)[@ prasadsunny 1](https://twitter.com/prasadsunny1)T43】2019 年 5 月 30 日上午 09:50[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1134034323772887040)