# 如何在 OS X 修复所有 Ruby“无法构建 gem 原生扩展”错误

> 原文：<https://dev.to/codenamev/how-to-fix-all-ruby-failed-to-build-gem-native-extension-errors-in-os-x-11n9>

```
cp /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg ~/Desktop && open ~/Desktop/macOS_SDK_headers_for_macOS_10.14.pkg 
```

[![How to fix all Ruby](img/cb1462c343aecf246c85dc69b070d1cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X_0KGFvW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.codenamev.com/conteimg/2019/07/fixed-it-1.gif)

嘣。现在开始移除你在`~/.bundle/config`中设置的所有讨厌的覆盖

所有的荣誉都归于了不起的若昂·罗查·达席尔瓦。一定要去[看看](https://silvae86.github.io/sysadmin/mac/osx/mojave/beta/libxml2/2018/07/05/fixing-missing-headers-for-homebrew-in-mac-osx-mojave/)关于你为什么需要这么做的详细文章。TLDR；Mac OS X 10.14 不再将包含库放在标准的`/usr/include`目录中并被 SIP 锁定。