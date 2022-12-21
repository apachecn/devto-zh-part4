# 脱机查看所有 HTTP 状态代码😜

> 原文：<https://dev.to/mukunzidd/view-all-http-status-codes-offline-4984>

如果你已经安装了 NodeJS，只需在命令行/shell/终端中输入`node`，然后输入`http.STATUS_CODES`。所有的状态码都会被返回，就像这样:

```
$ node
> http.STATUS_CODES
{ '100': 'Continue',
  '101': 'Switching Protocols',
  '102': 'Processing',
  '200': 'OK',
  '201': 'Created',
  '202': 'Accepted',
  ... } 
```

Enter fullscreen mode Exit fullscreen mode

也尝试`http.METHODS`只是为了更多的乐趣。