# jest for sinon stubss(如果不是 stub)

> 原文：<https://dev.to/2ezpz2plzme/jest-for-sinon-stubs-5bdb>

工作上，我们最近从摩卡、噶玛、柴、西农转换为 Jest。目前还不清楚
的直接替代物是什么

```
sinon.stub(object, 'methodName').callsFake(() => {}) 
```

Enter fullscreen mode Exit fullscreen mode

发现 Jest 提供了与
相似的功能

```
jest.spyOn(object, 'methodName').mockImplementation(() => {}) 
```

Enter fullscreen mode Exit fullscreen mode