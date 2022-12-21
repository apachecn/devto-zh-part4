# 学习 NextJs -第 7 天

> 原文：<https://dev.to/eperedo/learning-next-day-7-3dp4>

你[已经知道如何在你的 nextjs 页面](https://dev.to/eperedo/learning-nextjs-day-6-2odm)之间链接和传递数据。但是让我们面对现实吧，querystring 并不酷！所以今天我会更新那部分代码，把我们无聊的 url 变成一个干净的。

这是一个非常简单的任务，因为**链接**组件附带了一个**“as”**属性，允许您为 url 定义一个别名。

```
import Link from 'next/link';

function Home({ user  }) {
  // Other part of code omitted for reading purposes
  return (
    <Link href={`/about?userId=${user.id}`} as={`/users/${user.id}/about`} />
  )
} 
```

在上面的代码中我们正在创建一个 Link 组件，它的真实路径是 **/about？userId=${user.id}** ，但是用**作为**道具，你是在告诉 nextjs 你想在你的地址中显示那个 url。

如果您启动服务器并点击其中一个用户，您将看到浏览器中的新 url 类似于 **/users/1/about "而不是**/about？userId=1**

但是就像本大叔说的:**一个伟大的功能会带来巨大的意想不到的行为**如果你选择其中一个用户，然后重新加载页面，接下来会抛出一个 404 错误页面，这是正确的，因为 **/users/1/about** 只是一个别名，它实际上并不存在于服务器上。

所以我想第八天将会是非常有趣的一天。敬请期待！