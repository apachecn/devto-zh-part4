# 使用 never 类型使 switch 语句超类型安全

> 原文：<https://dev.to/carlrippon/make-switch-statements-super-type-safe-with-the-never-type-15l2>

[![Stop sign](img/4aae19ca30051b528014bbea8b002ee1.png "Photo by Wendelin Jacober from Pexels")](https://res.cloudinary.com/practicaldev/image/fetch/s--B36wlWB7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.carlrippon.com/wp-content/uploads/2019/09/broken-danger-daylight-1411397-1024x683.jpg)

## 问题

下面的代码是一个计数器组件的简单缩减器。它包含一个针对联合类型的`switch`语句，这是非常类型安全的: