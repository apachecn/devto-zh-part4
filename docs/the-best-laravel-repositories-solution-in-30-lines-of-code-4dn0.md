# 30 行代码的最佳 Laravel 存储库解决方案

> 原文：<https://dev.to/mratiebatie/the-best-laravel-repositories-solution-in-30-lines-of-code-4dn0>

### 一段 30 行的脚本如何最终为 Laravel apps 中的存储库模式提供一个好的解决方案。

### **问题**

Laravel 提供了一种很好的方式，通过一种对象关系映射------------------------------------------《雄辩》提供了一种很好的方式来查询您的数据库。当你的项目变得更大时，问题就出现了。实体和模型之间的区别开始变得模糊。让我给你举个例子:

我们有两个方法，一个叫`findByName`，一个叫`concatName`。