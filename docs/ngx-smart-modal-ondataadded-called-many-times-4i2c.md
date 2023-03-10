# 多次调用 ngx-smart-modal“on data added”

> 原文：<https://dev.to/nokiz/ngx-smart-modal-ondataadded-called-many-times-4i2c>

我是一个新手，用 angular8 和 ngx-smart-modal 库制作了一个客户列表，每个客户都有一个按钮可以把他从列表中删除。当我点击按钮时，我显示一个确认模式，当我点击接受时，将客户端代码传递给另一个组件，该组件搜索该代码并从数组列表中删除受影响的索引。

问题是:在打开的第一个模态中，“onDataAdded”被调用了一次(预期行为)。在第二个被调用两次(不是预期的)等等...

当我点击“删除”时，它从“客户端”组件触发该功能，打开确认模式并等待响应(onDataAdded):

```
delClientModal( index: number ) {
  this.clientToRemove = this.clients[index];
  this.clientService._passClient(this.clients[index]);
  this.ngxSmartModalService.getModal('delClientModal').open();
  this.ngxSmartModalService.get('delClientModal').onDataAdded.subscribe((data: any) => {
    const indexToRemove = this.clients.findIndex(i => i.clientcode === data);
    if (indexToRemove >=  0) {
      this.clients.splice(this.clients.findIndex(i => i.clientcode === data), 1);
    }
  });
} 
```

当我点击 accept 时，它从“client”组件中触发该函数，该组件将客户端代码发送到 clientService(用于 api 操作),并将“onDataAdded”的数据设置为 de modal 以捕捉它:

```
delClient(clientcode: string) {
    this.clientService._deleteClient(this.client);
    this.ngxSmartModalService.setModalData(clientcode, 'delClientModal');
    this.ngxSmartModalService.resetModalData('delClientModal');
  } 
```

我做错了什么？