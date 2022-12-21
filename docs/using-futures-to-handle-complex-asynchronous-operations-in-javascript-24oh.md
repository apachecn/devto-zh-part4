# 在 javascript 中使用 Futures 处理复杂的异步操作。

> 原文：<https://dev.to/vageez/using-futures-to-handle-complex-asynchronous-operations-in-javascript-24oh>

为了演示 javascript 的未来，我将引用 Fluture 库。一个幻想的土地服从一元替代承诺。

[Fluture](https://github.com/fluture-js/Fluture/tree/11.x) 提供了一个类似于 Promises 的控制结构。
很像承诺，期货代表异步操作(I/O)成功或失败产生的价值。

使用承诺从端点获取值是一个相当简单的操作。

它可能看起来像这样。

```
import axios from "axios";

var getToDo = id => axios.get(`https://jsonplaceholder.typicode.com/todos/${id}`)

getToDo(1)
 .then(({data}) => data)
 .catch(e => e)

// { userId: 1, id: 1, title: 'delectus autautem', completed: false } 
```

Enter fullscreen mode Exit fullscreen mode

使用未来从一个端点得到一个值也是相当简单的。看起来是这样的。

```
import axios from "axios";
import { tryP } from "fluture";

var getToDo = id => 
 tryP(() => axios.get(`https://jsonplaceholder.typicode.com/todos/${id}`))

getToDo(1).fork(err => err, ({ data }) => data)

// { userId: 1, id: 1, title: 'delectus autautem', completed: false } 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的地方。为了得到未来的结果，我们必须分叉。如果有错误，我们分叉的左侧将运行，类似于 catch。我们分支的右边将包含我们的结果，类似于。

期货允许我们将它们的结果链接并映射到其他期货中，或者在返回之前对期货的结果执行数据操作，以及在实际分叉之前捕捉错误并管理它们。

这里有一个例子。

```
import { tryP, of, ap } from "fluture";
import axios from "axios";

const loginRequest = email => password =>
  tryP(() => 
   axios({
    url :`https://www.fake.com/login`, 
    data : { email, password }
   })
)

const userDetailsRequest = id =>
  tryP(() => axios.get(`https://www.fake.com/userDetails/${id}`))

const login = email => password => loginRequest(email)(password)
 .chain({ data }) =>  userDetailsRequest(data.id))
 .map(({ data }) => formatData(data))
 .mapRej(err => formatError(err))

login('faker@gmail.com')('admin123').fork(err => err, userDetails => userDetails) 
```

Enter fullscreen mode Exit fullscreen mode

#### `.chain``.map``.mapRej`和`.chainRej`的区别

1.  chain:`.chain`的结果一定是未来
2.  地图:一个`.map`的结果不是未来
3.  map rej:`.mapRej`的结果不是未来，只有当未来失败时才会被触发
4.  chain rej:`.chainRej`的结果必须是未来，并且只有在未来失败时才会被触发

如果期货失败/出错，它将“短路”`.map`并且`.chain`将不运行，流程将被引导至`. mapRej`或`.chainRej`中由程序员定义的任何一个。

现在来看一些更复杂的东西。

最近有人让我写一个程序，为一篇博客文章获取评论。有一个请求返回了博客文章，其中包含一个 id 数组。每个 id 代表一条评论。每个评论都需要自己的请求。

所以想象一下，必须发出 100 个请求才能得到 100 条评论。

### (平行)[【https://github.com/fluture-js/Fluture/tree/11.x#parallel】T2

flutube 有一个 api 叫做`parallel`

Parallel 允许我们一次发出多个异步请求，不按特定的顺序进行解析，并在所有请求完成后返回结果。

这是它看起来的样子。

```
import { tryP, parallel } from "fluture";
import axios from "axios";

// Our Future
const getCommentRequest = comment_id =>
  tryP(() => axios.get(`https://www.fake-comments.com/id/${comment_id}`))
  .map(({ data }) => data);

// comments is an array of ID's
const getComments = comments => 
 parallel(Infinity, comments.map(getCommentRequest))

// Infinity will allow any number of requests to be fired simultaneously, returning us the results once all requests have completed.

// The result here will be an array containing the response from each request.
getComments.fork(err => err, comments => comments) 
```

Enter fullscreen mode Exit fullscreen mode

用数字代替无穷大。比如说 10 个，一次会获取 10 个注释，在检索到数组中的所有注释后进行解析。

在下一个例子中，想象这样一种情况，如果一些初始请求成功，may 必须获取一些对我们有用的数据。

### (美联社)[【https://github.com/fluture-js/Fluture/tree/11.x#ap】T2

将左侧未来中包含的函数应用于右侧未来中包含的值。如果其中一个期货被拒绝，那么产生的期货也将被拒绝。

假设我们需要获取一个用户帐户。如果账户找到了，我们就可以试着去找他们的朋友。如果我们找到他们的朋友，我们可以试着拿到他们朋友的照片。如果这些请求中的任何一个失败了，那么整个流程就会短路，我们就会陷入分叉的左侧，在那里我们可以处理错误。

```
import { tryP, of, ap } from "fluture";
import axios from "axios";

// Our Futures
const retrieveUserAccount = id =>
  tryP(() => axios.get(`https://www.fake.com/user/${id}`))

const retrieveUserFriends = id =>
  tryP(() => axios.get(`https://www.fake.com/friends/${id}`))

const retrieveUserFriendsPhotos = id =>
  tryP(() => axios.get(`https://www.fake.com/friendsPhotos/${id}`))

const retrieveUserInformation = id =>
  of(account => 
      friends => 
       friendsPhotos => {account, friends, friendsPhotos}) //All results returned
    .ap(retrieveUserFriendsPhotos(id)) // 3rd
    .ap(retrieveUserFriends(id)) // 2nd
    .ap(retrieveUserAccount(id)) // Fired 1st

retrieveUserInformation.fork(err => err, results => results) 
```

Enter fullscreen mode Exit fullscreen mode

期货允许我们很好地组合我们的异步操作。

(更多关于凹槽的信息)[[https://github.com/fluture-js/Fluture/tree/11.x](https://github.com/fluture-js/Fluture/tree/11.x)

感谢阅读！