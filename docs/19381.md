# 使用 localStorage、Angular & Akita 保持浏览器标签同步

> 原文：<https://dev.to/arielgueta/keeping-browser-tabs-in-sync-using-localstorage-angular-akita-p39>

在这篇文章中，我们将学习当用户打开多个标签页时，如何保持应用程序状态的同步。我们将通过使用 Web 存储 API 和 Akita 来实现这一点。需要基本的秋田知识。

## 创建联系人页面

对于我们的演示，我们将创建一个联系人页面，允许我们的用户添加、编辑和删除联系人。

```
ng add @datorama/akita
ng g af contacts 
```

上述命令将 Akita 添加到我们的项目中，并搭建我们需要的文件，实体存储、实体查询、模型和服务。

现在，我们来补充一下秋田的 [`persistState`](https://netbasal.gitbook.io/akita/enhancers/persist-state) :

```
// main.ts
import { persistState } from '@datorama/akita';

persistState({
  key: 'yourKey',
  include: ['contacts']
});

platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.log(err)); 
```

**来自文档:**`persistState`函数通过将应用程序的一些状态保存到 localStorage/sessionStorage 或任何实现 StorageEngine API 的地方，使您能够持久保存应用程序的一些状态，并在刷新后恢复它。

现在，我们唯一需要做的事情就是监听`window.storage` [事件](https://developer.mozilla.org/en-US/docs/Web/API/Window/storage_event)，当`localStorage`在另一个文档的上下文中被修改时，该事件就会触发。当它发出时，我们过滤我们感兴趣的`key`，并用新值调用`snapshotManager.setStoresSnapshot`方法。

```
import { untilDestroyed } from 'ngx-take-until-destroyed';
import { fromEvent } from 'rxjs';
import { snapshotManager } from '@datorama/akita';

class AppComponent {
  ngOnInit() {
    fromEvent<StorageEvent>(window, 'storage').pipe(
      filter(event => event.key === 'yourKey')
      untilDestroyed(this)
    ).subscribe(event => {
      snapshotManager.setStoresSnapshot(event.newValue, { skipStorageUpdate: true });
    });
  }
} 
```

[`setStoresSnapshot`](https://netbasal.gitbook.io/akita/enhancers/snapshot-manager) 方法采用类似于
的应用程序状态对象

```
{
  "storeName": state,
  "storeName": state
} 
```

并用给定的值更新每个商店。

试试看。在两个选项卡中打开您的应用程序，在其中一个选项卡中更改商店的值，并在第二个选项卡中看到它的反映。

这篇文章的灵感来自于它的 ngrx 版本。