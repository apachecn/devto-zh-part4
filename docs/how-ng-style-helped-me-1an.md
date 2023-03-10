# ng-style 如何帮助我

> 原文：<https://dev.to/andrewevans0102/how-ng-style-helped-me-1an>

[这篇文章最初发表在 https://www.rhythmandbinary.com](https://rhythmandbinary.com/post/How_ng-style_Helped_Me)

最近，我在一个 Angular 项目中尝试动态删除列表中的值。这非常简单，并且适用于标准的 css `text-decoration: line-through`，但是如果你想动态地应用这种风格，就很难做到。

环顾堆栈溢出，很多人都有办法解决这个问题。

我希望能够动态地浏览一行，并以一种我的应用程序可以在加载值时自动应用的方式保存它。

该应用程序由 [Firebase](https://dev.to/andrewevans0102/firebase-25m9-temp-slug-7845367) 托管，我想利用远程状态管理功能。使用 Firebase，您的数据使用 observables 来监听来自数据源的流。我会在[我的文章](https://blog.angularindepth.com/how-the-angular-fire-library-makes-firebase-feel-like-magic-1fda375966bb)中详细讨论这个问题。

回到我的故事…

所以我想动态地应用删除线。这实际上有点挑战性，因为我想在加载时应用穿透。有点像下面的:

[![](img/be50ea58371f002977c53018b5c57ca6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i4c1Foh5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://atevans85.files.wordpress.com/2019/06/grocery.png%3Fw%3D700)

我想在我的应用程序中包含以下内容:

1.  存储列表
2.  存储某些值有删除线的事实。
3.  加载时，我想动态应用删除线
4.  当用户单击复选框时，将应用直通

第一项非常简单，只是使用 AngularFire2 库，我写了一个小方法，它接收值并保存:

```
// async is not necessary here, but using it to control event loop
  async addItem() {
    const id = this.afs.createId();
    const groceryItem: GroceryItem = {
      value: this.createForm.controls.item.value,
      lineThrough: false,
      id: id
    };
    const createCall = await this.groceryItemsCollection.doc(id).set(groceryItem)
      .catch(() => new Error('Error when creating item'));

    if ( createCall instanceof Error) {
      return alert(createCall);
    }

    this.createForm.controls.item.setValue('');
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您注意到，在存储的值中，我包含了一个`lineThrough`布尔值。这是我存储删除线值的地方。这也解决了上面的第二个问题。

但是，怎么接这个呢？将 click 事件的侦听器添加到复选框，然后在保存时应用关联的值

监听模板 html:
中的值

```
<mat-checkbox (click)="linethrough(groceryItem)" [checked]="groceryItem.lineThrough"></mat-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

监听变化并更新本地值:

```
async linethrough(groceryItem: GroceryItem) {
    if (groceryItem.lineThrough) {
      groceryItem.lineThrough = false;
    } else {
      groceryItem.lineThrough = true;
    }
    const updateCall = await this.groceryItemsCollection.doc(groceryItem.id).set(groceryItem)
      .catch(() => new Error('Error when creating item'));

    if ( updateCall instanceof Error) {
      return alert(updateCall);
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

从 firebase onload 中动态读入值:

[![](img/9d43c0b2ff8795252692d9483e441eec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qqShqVVF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://atevans85.files.wordpress.com/2019/06/screen-shot-2019-06-27-at-11.42.51-am.png%3Fw%3D700)

WOAH！WOAH！`[ngstyle]`在那里做什么？

这是我的神奇解决方案。

如果你谷歌一下`[ngstyle]`,你会发现很多关于它如何动态地允许你应用样式的文章。

在这种情况下，我所做的是基于引入的值应用`text-decoration: line-through`。

这能让我做什么？

一切！J/k 但是这很酷。这一切基本上让我设置这个动态值，基于一个复选框，并完成了我的目标。

我建议你有空的时候在`[ngstyle]`上多看看。它非常酷，给你的模板带来了很大的灵活性。