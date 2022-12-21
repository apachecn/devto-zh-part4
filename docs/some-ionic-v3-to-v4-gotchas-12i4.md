# 一些离子 v3 到 v4 的问题

> 原文：<https://dev.to/walkingriver/some-ionic-v3-to-v4-gotchas-12i4>

不久前，我发表了一篇关于将应用从 Ionic v3 升级到 v4 的文章。在添加更多功能的同时，我发现有些问题并不完全直观，尤其是在消除警报之类的东西后更新 UI 时。这个问题很容易解决，但我花了一些时间才弄明白。这篇文章描述了我发现的一个解决方案。

# 这个问题

下面显示的代码是一个简单的确认对话框，用 Ionic v3 编写。询问用户“您确定要删除它吗？”如果用户选择`Cancel`，什么也不会发生。但是，如果用户选择了`Delete`，删除将在处理函数中发生，该函数使用`async`和`await`来等待删除完成。delete 函数返回更新后的游戏列表，该列表通过一个`*ngFor`绑定到组件的标记上。

```
async deleteGame(game: Game) {
  let alert = this.alertCtrl.create({
    header: 'Confirm delete',
    message: 'Are you really sure you want to delete this game?',
    buttons: [
      {
        text: 'Cancel',
        role: 'cancel',
        handler: () => {
          console.log('Cancel clicked');
        }
      },
      {
        text: 'Delete',
        handler: async () => {
          console.log('Delete clicked');
          await this.games = this.gameService.delete(game.id);
        }
      }
    ]
  });

  alert.present();
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Ionic v3 中，该功能工作正常。点击`Delete`按钮导致游戏被删除，UI 如预期刷新。

然而，当我把它放到我的 Ionic v4 项目中时，我立即开始遇到问题。首先，我在`create`行中发现了一个类型错误。对于 v4，AlertController 的`create`函数返回`Promise<HTMLIonAlertElement>`，而不是直接返回对象。解决这个问题很简单，只需在`create`线的前面加一个`await`。

# 不刷新界面

一旦编译器错误被解决，似乎一切都会正常工作。不幸的是，事实并非如此。虽然它编译得很好，但是在游戏被删除后，用户界面不会刷新。起初，我认为我的删除代码有错误，但事实并非如此。

事实上，当我点击应用程序中其他任何地方的空白处时，被删除的游戏会突然消失。这种行为让我找到了根本原因。

我发现删除正在发生，游戏数组正在正确更新，但不知何故在渲染过程之外。我立刻回想起处理 1.x 摘要循环的问题。

我首先想到的是给`Delete`按钮的处理函数添加一个`window.setTimeout`。两件事阻止了我。首先，它感觉像是一台组装机；第二，我不确定这是否可行。

# 修罗

我在网上寻找其他有同样问题的人，但似乎只有我一个人。这绝不令人鼓舞。所以很明显我错过了一些代码的微妙之处，也许它在 v3 中工作是一个幸运的意外。

我修复的线索来自于几乎所有的`AlertController`函数都返回承诺，而不仅仅是它的`create`函数。如果我要等更多的人呢。我在`present`函数中添加了一个`await`，但是行为并没有改变。

从那以后，我更仔细地查看了`AlertController` API 文档，注意到它有一些我可以调用的额外函数。看起来相关的是`onDidDismiss`，它返回一个解决(*惊喜！*)警报解除后。

更新后的代码如下。我加了三行，改了两行。

```
async deleteGame(game: Game) {
  let updatedGames: Game[];   // NEW 

  let alert = await this.alertCtrl.create({
    header: 'Confirm delete',
    message: 'Are you really sure you want to delete this game? This cannot be undone.',
    buttons: [
      {
        text: 'Cancel',
        role: 'cancel',
        handler: () => {
          console.log('Cancel clicked');
        }
      },
      {
        text: 'Delete',
        handler: async () => {
          console.log('Delete clicked');
          updatedGames = await this.gameService.delete(game.id); // CHANGE
        }
      }
    ]
  });

  await alert.present();  // CHANGE
  await alert.onDidDismiss(); // NEW 
  this.games = updatedGames || this.games; // NEW 
} 
```

Enter fullscreen mode Exit fullscreen mode

我没有立即更新组件的`games`数组，而是创建了一个本地数组来保存`delete`函数的结果。从那里，我决定将`await`的`present`功能化，然后立即`await onDidDismiss()`。在它返回之后，代码将组件的`games`数组设置为新列表(如果存在的话)或自身。是的，我可以使用条件句，但是我更喜欢更简洁的语法。

# 结论

这是正确的解决方案吗？有更好或更干净的解决方案吗？也许吧。这种方法看起来很简单，而且很有效。因此，我与世界分享，希望它可以帮助别人。如果你找到了更好的解决方案，请随时告诉我。

我遇到了与`PickerController`相同的问题。取消选取器后不刷新用户界面。它也有类似的 API，解决方案也是一样的。

# 参考文献

*   [离子框架警报控制器](https://ionicframework.com/docs/api/alert)
*   [离子框架拾取器控制器](https://ionicframework.com/docs/api/picker)

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。我还为 Pluralsight 编写了[课程，专门针对 Ionic 框架。](https://pluralsight.pxf.io/OnWrP)

*交叉贴自[走江湖博客](https://walkingriver.com/ionic-3-to-4-gotchas/)T3】*