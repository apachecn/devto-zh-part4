# 在 Angular 应用程序中使用 Url 作为唯一的真实来源

> 原文：<https://dev.to/leonelngande/using-the-url-as-the-single-source-of-truth-in-an-angular-application-5gd8>

最近，我一直在尝试将使用 url 作为我工作的 Angular 应用程序中唯一的真实来源的概念。今天我写了一些功能，很好地演示了这一点。

我们将要构建的是一个搜索栏组件，它将搜索输入作为查询参数推送到 url，并在页面重载时检索该值以恢复之前的搜索状态。

这里有一个[现场演示](https://leonelngande.github.io/material-icons-offline/)的链接以及一个 [Github 库](https://github.com/leonelngande/material-icons-offline)的链接。

[![Working demo](img/f4b9b981521178ac9ee0528a10ec2e92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m2DjA2vW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.leonelngande.cimg/uploads/search.gif)

让我们从搜索栏组件开始。

```
import {Component, OnInit} from '@angular/core';
import {ActivatedRoute, Router} from '@angular/router';

@Component({
  selector: 'app-search-bar',
  templateUrl: './search-bar.component.html',
  styleUrls: ['./search-bar.component.scss']
})
export class SearchBarComponent implements OnInit {

  searchValue: string;

  constructor(
    private router: Router,
    private activatedRoute: ActivatedRoute,
  ) { }

  ngOnInit() {
    this.searchValue = this.activatedRoute.snapshot.queryParams.search;
  }

  clearSearch() {
    this.searchValue = '';
    this.updateSearchQueryParam(this.searchValue);
  }

  updateSearchQueryParam(searchValue: string) {
    this.router.navigate(
      [],
      {
        relativeTo: this.activatedRoute,
        queryParams: {search: searchValue},
        replaceUrl: true,
        queryParamsHandling: 'merge'
      });
  }
} 
```

```
<form>
  <mat-form-field appearance="outline">

    <input type="search"
           placeholder="Search"
           matInput
           name="searchValue"
           [(ngModel)]="searchValue"
           (ngModelChange)="updateSearchQueryParam(searchValue)">

    <button mat-button *ngIf="searchValue" matSuffix mat-icon-button aria-label="Clear" (click)="clearSearch()">
      <mat-icon>close</mat-icon>
    </button>

  </mat-form-field>

</form> 
```

上面，我们使用 Angular 的双向数据绑定将搜索输入的值存储在`searchValue`中。必要时，我们同样有按钮来清除搜索输入。

有趣的部分是我们在模型改变时将搜索值推送到`updateSearchQueryParam`中的 url。该函数接受搜索值，并使用 Angular 路由器导航到当前路线，用新的搜索值添加或更新查询参数`search`，而无需重新加载页面。

下面是传递给`router.navigate`的每个导航额外功能的明细:

*   relative to:this . activate route-将当前激活的路线指定为根 URI，用于相对导航
*   replaceUrl: true -在替换历史中的当前状态时导航
*   queryParamsHandling: 'merge' -将新的查询参数与路由器链接中的当前查询参数合并，用于下一次导航
*   query params:{ search:search value }-将搜索查询参数设置为 URL

如果用户决定重新加载页面或共享 url，我们应该能够获得搜索值并恢复之前的状态。这就是 ngOnInit 中下面一行代码的作用:

```
this.searchValue = this.activatedRoute.snapshot.queryParams.search; 
```

它从激活路由的当前快照中选取搜索查询参数的值，并为其分配 searchValue，从而恢复先前的搜索状态。

这在您有另一个或多个使用搜索值的组件的情况下同样有用。在其他组件中，您只需在激活的路由上监听搜索查询参数的变化，如下所示。

```
export class MaterialIconsComponent implements OnInit {

  constructor(private activatedRoute: ActivatedRoute) { }

  ngOnInit() {
    // Listen for search route param changes
    this.activatedRoute.queryParams
      .pipe(
        map((queryParams: Params) => queryParams.search as string),
        distinctUntilChanged()
      )
      .subscribe((searchValue) => {
        // do something with the search value, maybe apply a filter to your list of items?
      });
  }
} 
```

使用这种设置，您可以变得非常有创造力:-)。

这篇博文到此为止，希望它在某些方面对你有所帮助。在博文中发现需要改进的地方或有问题吗？请随时在 Twitter 上联系我。

编码快乐！