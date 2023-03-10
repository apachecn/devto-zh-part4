# 如何为待办事项列表伪造 API

> 原文：<https://dev.to/znick1982/how-to-fake-your-api-for-todo-list-5e85>

在上一篇文章中，我讨论了为前端和后端开发人员提供 REST API 模拟工具的必要性，这是一个简单的层，可以让他们了解两者的通信接口。

下面是一个如何用 [https://fake.rest](https://fake.rest) 为 TODO 列表应用程序创建简单模拟的例子

假设我们有一个现成的 TODO list SPA 应用程序，但是后端不可用。例如，我们需要用以下方法来实施工作:

*   添加任务
*   删除任务
*   更新任务
*   获取任务列表

登录后让我们创建第一个端点模仿:
[![](img/08d7575104a36ca10618d0550506f67f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kgYGv3pf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8mooe9mxtdvsewja9upy.gif)

在“回复正文”中，我们添加以下模板并保存它。
[![](img/7cc5c55f210bc4d8cc54a7f62bf44375.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ab58tvfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2b8wu3y0qbudkt8uqbdx.png)

创建端点后，我们可以在任何 HTTP 客户端工具中测试它。
我选择了[失眠](https://insomnia.rest/download/)。
又得了:
[![](img/4f3a70e829d768eaa87cccd858658ed3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hpngpa05--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tehve2mf1vegmucajk9p.png)

为了简单起见，方法`task/delete`和`task/update`将使用相同的响应模板，所以我只是从`task/add`方法中复制了它

接下来我们需要的是生成任务列表:
[![](img/ef6209a1ed548f23a8f1e2754d62d8c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ScntJRNH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8s2dpbwc6zom4y5ms4us.gif)

让我们生成一个从 5 到 10 的任务数组:
[![](img/657f7eec657fe51333aac240d1053d1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TDipenCi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v6ifhh1dgr7whbwypsze.png)

再次检查失眠:
[![](img/d2c04b0ad18c73ae06d4dcf3ef901f49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b0HXhgo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmggof6yvl7utvbd0jnc.png)

看起来很有效。

你可以用下面的网址自己试试:

```
POST https://api.fake.rest/189bf93b-4d78-4f00-86ac-76d87cfccbd1/task/add
POST https://api.fake.rest/189bf93b-4d78-4f00-86ac-76d87cfccbd1/task/update
POST https://api.fake.rest/189bf93b-4d78-4f00-86ac-76d87cfccbd1/task/delete
GET https://api.fake.rest/189bf93b-4d78-4f00-86ac-76d87cfccbd1/task/list 
```

### 如何在代码中使用它

在我的前端项目中，我经常使用`axios`。

带有`axios`的片段可能是:

```
import axios from 'axios';

const ax = axios.create({
  baseURL: 'https://api.fake.rest/189bf93b-4d78-4f00-86ac-76d87cfccbd1/',
});

const sendTask = (url, task) =>
  ax
    .post(url, task)
    .then(res => res.data)
    .catch(err => {
      throw err;
    });

export const fetchTaskList = () =>
  ax
    .get(`task/list`)
    .then(res => res.data)
    .catch(err => {
      throw err;
    });

export const addTask = task =>
  sendTask(`task/add`, task);

export const updateTask = task =>
  sendTask(`task/update`, task);

export const deleteTask = task =>
  sendTask(`task/delete`, task); 
```

要在真正的 API 准备就绪时使用它，只需更改`baseURL`属性。

这是我第一次介绍假。休息我希望你喜欢它，并发现它有用。

如果你有关于如何改善服务的建议，请在评论中给我写信。

谢谢你。