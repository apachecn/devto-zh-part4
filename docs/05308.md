# 使用 Algolia 和 Firebase Angular 应用程序

> 原文：<https://dev.to/teamhive/using-algolia-with-firebase-angular-apps-28c6>

# 什么是 Algolia？

Algolia 是一个超级强大、可扩展的 API 服务，允许开发人员将不同形式的数据发送到他们的平台，并在其上快速执行搜索、排序和复杂的过滤查询。该服务非常快，通过使用副本索引来预先构建常见的查询条件，以尽可能快地发送回您的数据。

## 为什么要用 Algolia 搭配 Firebase？

Firebase 在查询数据结构的可访问性方面取得了很大进步，尤其是在 Firestore 中。即使有这些改进，它也有局限性，并且经常需要预先排序的数据，使用 Firebase 的语法 sugar 和 push id(push id 在其生成中包含一个日期哈希),并牺牲额外的读/写和直接的对象结构。Firebase 还[正式推荐](https://firebase.google.com/docs/firestore/solutions/search) Algolia 在 Firestore 中执行全文搜索操作。

# 入门

在这个工作示例中，我们将使用带有触发器的 Firebase Cloud 函数来帮助同步从 Firestore 到 Algolia 的数据更改。我们也将使用 Algolia 节点。JS 和 JavaScript 客户端模块，用于与它们的服务进行交互。

## Firebase 云功能

在您的函数目录中，您需要安装以下依赖项来利用 Algolia。

```
npm install --save algoliasearch @types/algoliasearch 
```

Enter fullscreen mode Exit fullscreen mode

对于这个例子，每当在我们的定制 Firestore 集合“users”中创建、更新或删除新的用户文档时，我们都将监听。

> 对于以下每个示例，您需要用通过 Algolia 的管理面板生成的您自己的访问令牌来替换`appId`和`apiKey`。

user.onCreate.ts

每次在 users 集合中创建新文档时，都会调度 userOnCreate 触发器。在下面的例子中，我们用应用的 id 和唯一的 API 键初始化 Algolia，并初始化我们想在 Algolia 中使用的索引。Algolia 建议按照您正在使用的实例/环境(即 dev_、prod_、staging_、next_)来命名索引。

我们还复制到索引，这样我们就可以按照用户名的升序或降序进行排序。Algolia 保留 objectID 用于关联他们世界中的记录；我们将使用新文档的路径 id。

```
import * as algoliasearch from 'algoliasearch';
import * as functions from 'firebase-functions';

export const userOnCreate = functions.firestore
   .document('users/{id}')
   .onCreate(async (change, context) => {
       const user = change.data();
       const client = algoliasearch('appId', 'apiKey');
       const index = client.initIndex('dev_users');
       await index.setSettings({
           replicas: [
               'dev_users_name_desc',
               'dev_users_name_asc'
           ]
       });
       return index.addObject({
           objectID: change.id,
           ...user
       });
   }); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

user.onUpdate.ts

userOnUpdate 触发器与 create 触发器非常相似。不同之处在于，我们不需要重新指定副本索引，因为一旦我们注册了它们；每当我们写入父索引(dev_users)时，它们会自动将数据推送到副本索引。

为了降低操作成本，Algolia 允许部分更新只更改索引对象的特定属性。

```
import * as algoliasearch from 'algoliasearch';
import * as functions from 'firebase-functions';

export const userOnUpdate = functions.firestore
   .document('users/{id}')
   .onCreate(async (change, context) => {
       const user = change.data();
       const client = algoliasearch('appId', 'apiKey');
       const index = client.initIndex('dev_users');
       return index.partialUpdateObject({
           objectID: change.id,
           ...user
       });
   }); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

用户.未读 ts

userOnDelete 触发器是最简单的操作，它调用 initialize 和 Delete 对象，通过我们前面定义的 objectID 删除 Algolia 对象。

```
import * as algoliasearch from 'algoliasearch';
import * as functions from 'firebase-functions';

export const userOnDelete = functions.firestore
   .document('users/{id}')
   .onCreate(async (change, context) => {
       const client = algoliasearch('appId', 'apiKey');
       const index = client.initIndex('dev_users');
       return index.deleteObject(change.id);
   }); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

将所有这些常量导出到您的根 index.ts 文件。这将在您构建和部署时将它们注册为新的 Firebase 云函数。此时，任何时候您在 Firestore 中更改文档(直接通过 Firebase 控制台或使用您的应用程序)，都会触发这些功能将数据推送到 Algolia 并与之同步。

```
firebase deploy --only functions:userOnCreate,functions:userOnUpdate,functions:userOnDelete 
```

Enter fullscreen mode Exit fullscreen mode

## 应用端

> 您可以在环境文件中存储 Algolia 的仅搜索访问令牌(这不同于云函数中使用的 apiKey ),以便轻松访问/导入它。

创建一个简单的服务来轻松地与您的 Algolia 索引进行交互。

user.service.ts

```
import * as algoliasearch from 'algoliasearch';

@Injectable()
export class UserService {

   client: algoliasearch.Client;

   init(config: {
       appId: string,
       apiKey: string
   }) {
       this.client = algoliasearch('appId', 'apiKey');
   }

   fetchUsers(options: algoliasearch.QueryParameters) {
       const userSearch = this.client.initIndex('dev_users');
       return userSearch.search(options);
   }

   fetchUsersByNameAsc(options: algoliasearch.QueryParameters) {
       const userSearch = this.client.initIndex('dev_users_name_asc');
       return userSearch.search(options);
   }

   fetchUsersByNameDesc(options: algoliasearch.QueryParameters) {
       const userSearch = this.client.initIndex('dev_users_name_desc');
       return userSearch.search(options);
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

在您的组件中，提供 UserService 并进行以下方法调用来测试从 Algolia 返回的响应。

```
async ngOnInit() {
    this.init({ appId: 'foo', apiKey: 'bar' });
    const res = await this.fetchUsers({
        page: 0,
        length: 10,
        query: 'Sean'
    });
    console.log('res', res);
} 
```

Enter fullscreen mode Exit fullscreen mode

此方法调用将尝试加载结果的第一页，最多 10 条记录，这些记录具有与“Sean”匹配的可搜索属性。

# 最后的想法

不要太深入 Algolia 的客户端，明确地专注于同步数据和快速记录信息；我们可以看到 Algolia 作为一个强大的接口来接收我们需要的精确数据。

在我们在 [Hive](https://www.teamhive.com) 上的实现中，我们使用 Algolia 来处理分页的管理表、无限滚动体验、按特定条件预过滤集合记录以及对表数据进行排序。您还可以将 Algolia 用作只读数据库，只存储/同步客户端应该有权访问的文档。当使用软删除等概念时，这是非常强大的，在这种情况下，您可以在 Firestore 中用一个`deletedAt`时间戳标记一个文档，并从 Algolia 中删除该对象。通过这样做，您总是可以恢复文档，但是来自 Algolia 的所有查询逻辑都将文档视为被删除。

## 附加资源

*   [Algolia 文档](https://www.algolia.com/doc/)
*   [Algolia 定价](https://www.algolia.com/pricing/)