# 需要帮助清理这段代码，它修改了子域。

> 原文：<https://dev.to/dechamp/need-help-cleaning-up-this-code-which-modifies-the-subdomain-37i3>

目标是得到一个子域，并把它换成另一个。我觉得应该有更干净的方法来做这件事。帮我一把！

```
var url = window.location;

// example doamin would have a subdomain so https://auth.domain.ext
var domain = url.host;
var domainParts = domain.split('.');
var mainDomainParts = domainParts.slice(domainParts.length - 2, domainParts.length);
mainDomainParts.unshift('admin');
var adminDomain = mainDomainParts.join('.');
var fullAdminDomain = url.protocol + '//' + adminDomain; 
```

Enter fullscreen mode Exit fullscreen mode