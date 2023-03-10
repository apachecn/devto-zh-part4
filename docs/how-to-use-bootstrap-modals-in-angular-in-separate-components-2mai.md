# 如何在单独的组件中使用 Angular 中的引导模式

> 原文：<https://dev.to/fanmixco/how-to-use-bootstrap-modals-in-angular-in-separate-components-2mai>

正如许多人可能经历过的那样，在 Angular 中有一些引导控件不容易使用，这就是为什么一个好的解决方案是: **ng-Bootstrap** 。

```
npm install --save @ng-bootstrap/ng-bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

然而，并不是所有的控件都这么容易使用，当你想把模态分解成多个组件时，会出现一种复杂的情况。常规模态如下所示:

**HTML 代码:**

```
<a (click)="openModal(contentModal)" class="nav-link">Open modal</a>

<ng-template #contentModal let-c="close" let-d="dismiss">
  <div class="modal-header">
      <h4 class="modal-title" id="modal-primary-title">Title</h4>
      <button type="button" class="close" aria-label="Close" (click)="d('Cross click')">
        <span aria-hidden="true">&times;</span>
      </button>
  </div>
  <div class="modal-body centered">
    <!--Body-->
  </div>
</ng-template> 
```

Enter fullscreen mode Exit fullscreen mode

**打字稿代码:**

```
openModal(contentModal) {
    this.modalService.open(contentModal);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一个或两个情态动词，并且它们背后没有任何逻辑，除了在 About 情态动词中突然出现之外，这是很好的，但是如果你有 3 个以上的情态动词有完整的逻辑呢？

很多时候，这些都是业务需求，这可能会给你的代码带来很大的混乱。在这种情况下，前面的解决方案根本不可行。让我们不要谈论可伸缩性或许多其他相关的例子。

经过多次尝试，我设计了一个解决方案，帮助将模态分解成独立的组件。

第一步是创建新组件:

```
ng generate component ModalComponent 
```

Enter fullscreen mode Exit fullscreen mode

之后，在`app.module.ts` :
的`entryComponents`部分注册你的模态

```
entryComponents: [
    ModalComponent,
], 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将旧的模型复制到新的组件中，并删除这两行:

```
<ng-template #contentModal let-c="close" let-d="dismiss">
</ng-template> 
```

Enter fullscreen mode Exit fullscreen mode

应该是这样的:

```
<div class="modal-header">
    <h4 class="modal-title" id="modal-primary-title">Title</h4>
    <button type="button" class="close" aria-label="Close" (click)="d('Cross click')">
        <span aria-hidden="true">&times;</span>
    </button>
</div>
<div class="modal-body centered">
    <!--Body-->
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，click 事件的逻辑以及如何调用它有了一个小小的变化:

这是新的 HTML 代码:

```
<a class="nav-link" (click)="openModal()">About</a> 
```

Enter fullscreen mode Exit fullscreen mode

这是新的类型脚本事件:

```
openModal() {
    //Here you define the name of your component
    this.modalService.open(ModalComponent);
    //This section is if you want to have any variable to initialize
    //compConst.componentInstance.weight = undefined;
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，在您的新组件中，您需要添加 change 您的`constructor`并添加一个`NgbActiveModal`的实例。

```
constructor(public activeModal: NgbActiveModal) { } 
```

Enter fullscreen mode Exit fullscreen mode

另一个重要的变化是在关闭模态的逻辑中，需要改成这样:

```
<button type="button" class="close" aria-label="Close" (click)="activeModal.dismiss('Cross click')">
    <span aria-hidden="true">&times;</span>
</button> 
```

Enter fullscreen mode Exit fullscreen mode

你需要把旧的:`(click)="d('Cross click')"`改成`(click)="activeModal.dismiss('Cross click')"`

有了这些变化，它会像魅力一样工作。我从这里得到了一些启发:

[https://stackblitz.com/edit/angular-uwtgs6](https://stackblitz.com/edit/angular-uwtgs6)

### 关注我:

| 商务化人际关系网 | 油管（国外视频网站） | 照片墙 | 网络先知 | 分享您的故事 |
| --- | --- | --- | --- | --- |
| [![LinkedIn](img/b501f070e2b1152fca94fce6ba1c10a1.png)](https://bit.ly/3xLCmvb) | [![YouTube](img/8c0c02bd24ea88f577be06d32e095211.png)](https://youtube.com/c/FedericoNavarrete) | [![Instagram](img/fb6ab53495291eee24fb9a2cc9f19fb0.png)](https://www.instagram.com/federico_the_consultant) | [![RedCircle Podcast](img/8c170a26d1702d958dc4f2c4a1c40e3e.png)](https://redcircle.com/shows/cyber-prophets) | [![RedCircle Podcast](img/8c170a26d1702d958dc4f2c4a1c40e3e.png)](https://redcircle.com/shows/sharing-your-stories) |

[![sponsor me](img/226830d02a39549ee59aecb63f3d35cd.png)](https://www.buymeacoffee.com/fanmixco)