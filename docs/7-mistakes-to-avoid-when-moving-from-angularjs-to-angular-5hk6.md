# 从 AngularJS 到 Angular js 要避免的 7 个错误

> 原文：<https://dev.to/angular/7-mistakes-to-avoid-when-moving-from-angularjs-to-angular-5hk6>

你从写 AngularJS 转向 Angular apps 了吗？有很多东西需要学习，但是先看看这个关于避免错误的快速指南吧！

升级任何项目都是一项艰巨的任务，AngularJS 和 Angular 之间的巨大变化几乎就像是要学习一个全新的框架！幸运的是 Angular 是对 AngularJS 的重大改进，如果有一件事值得兴奋，那就是不再与 UI 路由器争斗。；)几个月前，我重读了约翰·帕帕的 AngularJS styleguide ,回想那些实际上不再需要的模式和优化真是太棒了，因为它们现在已经被 Angular 解决了。有一点学习曲线和一些“gotchyas ”,你可能会遇到，所以这里有一个快速指南，避免从 AngularJS 到 Angular development 的错误。

## 1。不知道如何搜索 Angular

谷歌的品牌战略是将 Angular 版本 2 和上面的所有东西区分为“Angular”，将 Angular 1 区分为“AngularJS”。不幸的是，当谷歌搜索并试图区分结果时，这是没有帮助的。搜索“Angular 2 xyz”通常会更快地找到您需要的结果。演示代码工具也发生了变化——我们用 Codepen 替换了 Stackblitz，这非常棒。

## 2。不使用 Angular CLI 安装&

我清楚地记得当时我在面试一个前端开发主管时，与我未来的 CTO 关于 Angular CLI 和 webpack 的对话。我刚从 AngularJS 的工作中回来，CTO 提到他们还没有建立单元测试，前端需要做很多工作。我是那个努力从 Grunt 转换到 Webpack 并在我以前的 AngularJS 项目上建立单元测试的人，当提到这一点时，我无法相信他说 Angular CLI“只是工作”。“Just works”与我使用任何 Webpack 的经验都不匹配，我已经习惯了数小时的调试，加载程序的痛苦，以及当需要进行任何更改时配置调整的一般战斗。

[![Bane quote moulded by webpack](img/a00751ed12bca2a279a79467995ae8a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bY9zZAfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abepiy49sa4x25y3mezn.jpg)

是的，我的首席技术官没有撒谎，这是一个很棒的产品，而且确实有效。当我接受这份工作并接手我的第一个 Angular 项目的前端时，它已经由后端开发人员建立起来了，没有阴影，甚至没有使用 css 编译器。我花了大概一天的时间做 UI 修改，然后就哭了，需要换成更少的。我卷起袖子，装上文件，准备战斗，然后跑:

```
ng config defaults.styleExt=less 
```

我把文件扩展名从。css 到。less 更新了组件中的引用，一切都正常了。没有编译器错误，没有“缺少合适的加载程序”的消息，只是一个无缝的开发体验。我很震惊。角度变化。

需要做一个新的组件？

```
ng g component my-sweet-component 
```

需要创建一个新的服务来向服务器请求一些数据吗？

```
ng g service get-all-the-datas 
```

需要第三方库中的选项卡 UI 元素吗？

```
ng add ngx-bootstrap  --component tabs 
```

有了 [Angular CLI](https://cli.angular.io/) 生活是美好的，所以花时间去熟悉它吧——比起手动构建新的组件&模块，你会节省大量的击键次数，编译器错误也更少。当你真正感到舒适时，检查一下[原理图](https://medium.com/@tomastrajan/total-guide-to-custom-angular-schematics-5c50cf90cdb4)的威力！

## 3。没有花时间学习 TypeScript 基础知识

我有一个坏习惯，想马上投入到项目中并开始修复 bug，这和我第一次从做 AngularJS 工作转到一个前端一团糟的 Angular 项目没有什么不同。回想起来，花点时间浏览 TypeScript 基础知识与我通常的“边学边做”策略相比，效率会高得多，而且我在调试编译器早期抛出的 TypeScript 错误上花费的时间也会少得多。

我知道使用 TypeScript 可能看起来有限制和要求，但是熟悉并开始使用它并不需要你想象的那么多时间，如果你一直在编写 ES6，你已经完成了一半。在你知道之前，你会喜欢你能够如此快速地在头脑中解析和使用带注释的代码。您还可以快速利用内置的 Angular 类和接口。

https://www.typescriptlang.org/的 [文档很棒，或者为了获得一些快速的实践学习经验，您可以参加](https://www.typescriptlang.org/) [Bitovi 打字培训课程](https://www.bitovi.com/academy/learn-typescript.html)，大约 3 个小时！(不打算说谎，我希望在我第一次转向写 Angular 时有这个培训)

## 4。受到语法变化的影响

有趣的是，我认为从 AngularJS 迁移到 Angular js 时最吸引我的是模板循环语法的变化。在我放弃并开始使用 [Angular VS 代码片段](https://marketplace.visualstudio.com/items?itemName=Mikael.Angular-BeastCode)之前，我的浏览器内编译器错误的绝大多数来源是由于键入“让季节中的季节**”。我现在开心多了。=)我非常提倡使用代码片段，尤其是在我们知道我们想要代码做什么，但是却忙得不可开交，试图在第一杯咖啡喝完之前编写代码，或者为了同样的目的转向稍微不同的语法的情况下。**

在 AngularJS 中迭代

```
 <ul class="seasons">
  <li class="season" ng-repeat="season in seasons">
    Season {{season.seasonNumber}}
  </li>
</ul> 
```

角度迭代

```
 <ul class="seasons">
  <li class="season" *ngFor="let season of seasons">
    Season {{season.seasonNumber}}
  </li>
</ul> 
```

## 5。没有学习 RxJS 的基础知识

在反应式编程中，RxJS 可能看起来势不可挡，但是从小步开始是可以的。要学习的一些基本概念是可观察性、订阅和运算符。

**可观察值**是多个值随时间变化的惰性集合。

**订阅**是我们“订阅”可观察物以获取其价值的方式。

操作符是对可观察对象进行操作的方法。重要的是要知道运营商不改变可观测量，但返回新的可观测量的基础上，目前的可观测量。

为了演示可观测量和订阅，让我们看看在 AngularJS 中创建服务和获取数据，以及在 Angular js 中处理承诺和可观测量。

### 发出 HTTP 请求

在构建 AngularJS 应用程序时，您可能非常熟悉创建服务，使用$http 发出 http 请求，并返回承诺:

```
'use strict';

SeasonService.$inject = []

class Seasons {
  constructor($http, $ENV, SeasonService) {
    this.$http = $http;
    this.env = $ENV;
  }
  getSeasons() {
    return this.$http({
      method: 'GET',
      url: this.env.APIurl + '/seasons'
    }).then((results) => {
      return results.data
    })
  }

  getSeason(season_id) {
    return this.$http({
      method: 'GET',
      url: this.env.APIurl + '/seasons/' + season_id
    }).then((results) => {
      return results.data
    })
  }
}

Seasons.$inject = ['$http', '$ENV', 'SeasonService'];

export default angular.module('seasons', [])
  .service('Seasons', Seasons)
  .name;

function seasonsView() {
  return {
    template: require('./partials/seasonsview.html'),
    controllerAs: 'sv',
    controller: seasonsViewCtrl
  }
}

function seasonsViewCtrl($scope, Seasons) {
   let sv = this;

   sv.showSeasons = function() {
     Seasons.getSeasons().then((response) => {
        sv.seasons = response;
      }).catch(() => {
        sv.errorMessage = 'failed to fetch seasons. stop trying to make fetch happen';
      })
   }
}
seasonsViewCtrl.$inject = ['$scope', 'Seasons'] 
```

在 Angular 中，HttpClient API 将返回收集的数据的可观察值。然后，我们可以在组件中订阅它，以获得最新的值。

季节服务

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class SeasonService {
  public apiUrl: string = 'http://www.nokeynoshade.party/api';

  constructor(private http: HttpClient) { }

  getSeasons() {
    return this.http.get(this.apiUrl + '/seasons');
  }

  getSeason(seasonId: number) {
    return this.http.get(this.apiUrl + '/seasons/' + seasonId);
  }
} 
```

季节.组件. ts

```
import { Component, OnInit } from '@angular/core';
import { SeasonService } from './season.service';

@Component({
  selector: 'dr-seasons',
  templateUrl: './seasons.component.html',
  styleUrls: ['./seasons.component.less']
})
export class SeasonsComponent implements OnInit {
  public seasons: any = [];

  constructor(
    private seasonService: SeasonService
    ) { }

  ngOnInit() {
    this.seasonService.getSeasons().subscribe((seasons) => {
      this.seasons = seasons;
    })
  }
} 
```

反应式编程是一个很大的思维转变，但是为了探索它的力量，让我们看一个常见的场景——我们有一些想要获取的数据，我们有一些想要显示给用户的加载 UI 组件，让他们知道我们正在获取这些数据。

重温上面的代码，我们可能会这样做:

季节.组件. ts

```
import { Component, OnInit } from '@angular/core';
import { SeasonService } from './season.service';

@Component({
  selector: 'dr-seasons',
  templateUrl: './seasons.component.html',
  styleUrls: ['./seasons.component.less']
})
export class SeasonsComponent implements OnInit {
  public seasons: any = [];
  //boolean value to indicate loading status
  public seasonsLoading: boolean = false;
  constructor(
    private seasonService: SeasonService
    ) { }

  ngOnInit() {
    //setting value to true before seasons HTTP request is made
    this.seasonsLoading = true;
    this.seasonService.getSeasons().subscribe((seasons) => {
      this.seasons = seasons;
      //setting value to false once we have the data
      this.seasonsLoading = false;
    })
  }
} 
```

我们可能有这样的模板代码:

```
<h2>Seasons</h2>
<ng-container *ngIf="seasons.length">
  <div class="row" *ngFor="let chunk of seasons | chunks: 4">
    <mat-card *ngFor="let season of chunk" class="season-card">
      <mat-card-header>
        <mat-card-title>Season {{season.id}}</mat-card-title>
      </mat-card-header>
      <img routerLink="/seasons/{{season.id}}" mat-card-image src="{{season.image_url}}" alt="Season {{season.id}} promo photo">
    </mat-card>
  </div>
</ng-container>
<ng-container *ngIf="seasonsLoading"><mat-spinner></mat-spinner></ng-container> 
```

反应式方法应该是这样的:

```
import { Component, OnInit } from '@angular/core';
import { SeasonService } from './season.service';
import { map, startWith } from 'rxjs/operators';
import { Observable } from 'rxjs';

function sortBySeasonNumber(a,b) {
  const seasonA = parseInt(a.seasonNumber, 10);
  const seasonB = parseInt(b.seasonNumber, 10);
  if (seasonA < seasonB)
    return -1;
  if (seasonA > seasonB)
    return 1;
  return 0;
}

@Component({
  selector: 'dr-seasons',
  templateUrl: './seasons.component.html',
  styleUrls: ['./seasons.component.less']
})
export class SeasonsComponent implements OnInit {
  public seasons$: Observable<any>;

  constructor(
    private seasonService: SeasonService
    ) { }

  ngOnInit() {
    this.seasons$ = this.seasonService.getSeasons().pipe(
      map((response) => {
        return {
          value: response.sort(sortBySeasonNumber),
          isLoading: false
        }
      }),
      startWith({isLoading: true, value: []})
    );
  }
} 
```

在这段代码中，我们现在有一个正在使用运算符的可观察对象。我们使用`map`操作符从我们的`getSeasons`方法获得响应，并将其映射到我们的`value`键(我也调用了一个排序函数，因为 API 不按时间顺序返回季节)，并将我们的`isLoading`键设置为 false。我们使用`startWith`操作符将初始值设置为空数组，并将`isLoading`设置为真。

在我们的 HTML 标记中，我们将使用 Angular 的[异步管道](https://angular.io/api/common/AsyncPipe)来订阅我们的`seasons$`可观察对象。

```
<h2>Seasons</h2>
<ng-container *ngIf="(seasons$ | async).value.length; else loading">
  <div class="row" *ngFor="let chunk of (seasons$ | async).value | chunks: 4">
    <mat-card *ngFor="let season of chunk" class="season-card">
      <mat-card-header>
        <mat-card-title>Season {{season.seasonNumber}}</mat-card-title>
      </mat-card-header>
      <img routerLink="/seasons/{{season.id}}" mat-card-image src="{{season.image_url}}" alt="Season {{season.id}} promo photo">
    </mat-card>
  </div>
</ng-container>
<ng-template #loading>
  <mat-spinner></mat-spinner>
</ng-template> 
```

听起来不像是破纪录，但在 Bitovi，我们也建立了一个 [RxJS 培训课程](https://www.bitovi.com/academy/learn-rxjs.html)来帮助你在角度环境中快速使用 RxJS。

## 6。没有意识到服务依赖注入的细微差别

在 AngularJS，你与服务和工厂打交道。工厂允许您根据自己的需要创建服务的新实例。在 Angular，只有服务，你提供服务的方式很重要。

例如，下面的代码在应用程序的根级别创建了一个服务的共享实例——它将作为所有组件的依赖项。当 Angular 查找依赖关系时，它首先检查依赖关系的现有实例，如果依赖关系尚不存在，则创建一个实例。

```
@Injectable({
 providedIn: 'root',
})
export class MyService {
} 
```

如果您为单个模块注册了一个提供程序，它将只能在该模块中使用。

```
@NgModule({
  providers: [
  MyService
 ],
 ...
})
export class MyChildModule { } 
```

如果您需要一个新的服务实例，您可以在特定的组件提供者中注册它。

```
@Component({
  selector:    'service-list',
  templateUrl: './service-list.component.html',
  providers:  [ MyService ]
}) 
```

## 7。不了解变更检测

[![$scope.$watch all the things](img/3bbca50d1b269dcb0327507e4a7f6172.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fIc7EADD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tup2f1gbd80xy286bx0m.jpg)

在 AngularJS 中到处散布`$scope.$watch`很容易降低性能——如果这是你的团队使用的拐杖，那么转移到 Angular 是确保每个人都理解变化检测如何工作的好时机。

老实说，在 Angular 中，如果你正在处理 Observables，你很少会遇到 UI 没有更新以反映你正在处理的数据的变化的情况——我想不出在 Angular 开发中有什么实例需要调用类似于`$scope.$watch`的东西，这种感觉很好！当您开始构建 Angular 应用程序时，我鼓励您使用 reactive forms API——与您习惯的典型的`[ngModel]`方法相比，当与表单元素交互并在 UI 中显示更改时，这是一种更好的体验。

当你的团队走向真正的反应式编程时，需要一种方法来告诉 Angular 去监听变化/进行更新将会是一个更小的问题。现在最关键的事情是记得取消订阅你的 Observables，以防止内存泄漏。

Angular 提供了一些生命周期挂钩来帮助您管理，下面是一些比较常见的挂钩:

```
@Component({
  selector: 'my-component',
  template: `<p>Hello world</p>`
})
class MyComponent implements OnInit, OnChanges, DoCheck, OnDestroy {

  ngOnChanges(changes: SimpleChange) {
    //Called before ngOnInit() and whenever one or more data-bound input properties change.
  }

  ngOnInit() {
    //Called once, after the first ngOnChanges().
  }

  ngDoCheck() {
    //Called during every change detection run, immediately after ngOnChanges() and ngOnInit().
  }

  ngOnDestroy() {
    //Called just before Angular destroys the directive/component.
  }
} 
```

为了更深入地理解 Angular 中变化检测背后的机制，我非常喜欢这些解释区域以及 Angular 如何使用它们的文章。

*   [了解区域](https://blog.thoughtram.io/angular/2016/01/22/understanding-zones.html)
*   [角度 2 中的区域](https://blog.thoughtram.io/angular/2016/02/01/zones-in-angular-2.html)
*   [角度 2 变化检测说明](https://blog.thoughtram.io/angular/2016/02/22/angular-2-change-detection-explained.html)

## 总结

Angular 生态系统中有很多东西需要学习，Ivy 即将带来令人兴奋的变化，因此没有比现在更好的时间从 AngularJS 转向 Angular，我很高兴帮助您度过难关！在[bitovi.com](https://www.bitovi.com/frontend-javascript-consulting/angular-consulting%20target=)了解更多信息。