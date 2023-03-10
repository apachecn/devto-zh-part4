# 使用“async as”清理角度模板

> 原文：<https://dev.to/vanbalken/use-async-as-to-cleanup-your-angular-templates-3mk3>

异步管道的结果可以存储在一个本地变量:

```
<span *ngIf="person$ | async as person">
    Hello {{ person.name }}
</span> 
```

局部变量可以在`ngIf`的范围内使用。这可以减少对额外的异步管道和`?`操作符的需求。

重要的部分是`as`，你可以用它将`ngIf`的条件结果存储在一个局部变量中(参见:[角度文件](https://angular.io/api/common/NgIf#storing-a-conditional-result-in-a-variable))。

我是通过阅读[用 NgIf 和异步管道@ Ultimates 课程](https://ultimatecourses.com/blog/angular-ngif-async-pipe)来了解这个问题的。我建议阅读它以获取更多信息。

> [棱角风格指南](https://angular.io/guide/styleguide)没有提到如何命名你的可观测量。但是在 RxJS 库的文档中，他们说:用一个尾随的“$”来命名可观测物会很方便( [Angular Docs](https://angular.io/guide/rx-library#naming-conventions-for-observables) )。