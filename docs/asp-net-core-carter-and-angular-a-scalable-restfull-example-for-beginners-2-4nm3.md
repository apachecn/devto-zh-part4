# ASP.NET 核心，卡特和 Angular，一个可扩展的 RESTfull 初学者的例子？(2)

> 原文：<https://dev.to/sebalr/asp-net-core-carter-and-angular-a-scalable-restfull-example-for-beginners-2-4nm3>

大家好。如果你正在读这篇文章，也许你已经读完了我之前的教程:

[![sebalr image](img/f28b43c2e7e69877bd882083a04d37f3.png)](/sebalr) [## ASP.NET 核心，卡特和 Angular，一个可扩展的 RESTfull 初学者的例子？(1)

### 塞巴斯蒂安·拉里厄 6 月 9 日 10 分钟阅读

#angular #netcore #api #tutorial](/sebalr/asp-net-core-carter-and-angular-a-scalable-restfull-example-for-beginners-1-pgk)

或者只是想要一个如何编写可扩展的 Angular 应用程序的好例子。请记住，我们正在构建和过度设计 to-cook 应用程序(就像 to-do，但有食物),从一些基本结构开始，稍后进行重构，使其可扩展。

这里是本教程的 [Github 报告](https://github.com/sebalr/recipes-frontend)。

让我告诉你，我的母语不是英语，所以你可能会发现一些错误，请让我知道，我会纠正他们

让我从一些基本的想法开始，我们喜欢在各种角度的项目后在我的公司里遵循这些想法。

1.  具有基本布局组件、导航栏、侧栏和页脚的核心文件夹。
2.  与 API 通信的共享后端服务。
3.  封装功能的模块(不仅仅是组件),即 RecipeModule 与配方的 CRUD 组件。
4.  类(dto)在后端和前端(而不是接口)之间共享数据。
5.  封装 dto 以在中使用的视图模型。ts 和. html。
6.  改为 Async-Await 以使代码看起来更好。
7.  显示后端错误消息的全局异常处理程序(而不是捕捉每个 http 请求)。

在本教程中，我们将使用 [CLI](https://cli.angular.io/) 使用这些概念中的一些(但不是全部，因为这是一个系列)来构建 starter Angular 模板。

让我们从一个新的角度项目开始。如果没有 CLI，首先安装它，然后创建一个新应用程序，当它询问路由模块和 sass 样式表时，选择 yes。

```
npm install -g @angular/cli
ng new cook-app
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? 
  CSS 
❯ Sass   [ http://sass-lang.com   ] 
  Less   [ http://lesscss.org     ] 
  Stylus [ http://stylus-lang.com ] 
```

现在，我们将安装引导程序并创建一些文件夹:

```
cd cook-app
npm install bootstrap
cd src/app
mkdir shared
mkdir dtos
cd shared
touch constants.ts
mkdir backend-services 
```

在常量中，我们将保存生产和开发环境的 API url，以便打开文件并粘贴:

```
import { environment } from 'src/environments/environment.prod';

export const apiUrl = environment.production ? 'http://localhost:5000' : 'http://localhost:5000'; 
```

让我们建立与 API 的通信。转到 dtos 文件夹并创建一个 *recipe.dto.ts* ，它将具有与后端模型相同的属性和一个从 json 生成它的静态方法

```
export class RecipeDTO {

  id: number;
  title: string;
  description: string;

  public constructor() { }

  public static prepareFromJson(json: any): RecipeDTO {
    const recipe = new RecipeDTO();

    recipe.id = json.id ? json.id : 0;
    recipe.title = json.title ? json.title : undefined;
    recipe.description = json.description ? json.description : undefined;

    return recipe;
  }

} 
```

现在使用 CLI 在后端服务文件夹中创建一个服务。

```
ng g service recipeBackend 
```

在这个服务中，我们将编写与后端通信的方法，让我们从获取所有食谱开始:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { apiUrl } from 'src/app/shared/constants';
import { RecipeDTO } from '../dtos/recipe.dto';

@Injectable({
  providedIn: 'root'
})
export class RecipeBackendService {

  constructor(private httpClient: HttpClient) { }

  public async getAll(): Promise<Array<RecipeDTO>> {
    const recipes = new Array<RecipeDTO>();

    const res = await this.httpClient.get<any>(`${apiUrl}/recipe`).toPromise();

    for (const item of res) {
      recipes.push(RecipeDTO.prepareFromJson(item));
    }
    return recipes;
  }

} 
```

正如我之前告诉你的，我使用 await 是因为代码看起来更干净。然后()链。您可能想知道没有 *try-catch* 会出现什么错误。好吧，现在我们假设一切正常(遗憾的是这在现实生活中从未发生过)，在下一篇文章中，我们将实现一个异常处理程序，在一个地方处理所有的错误。

在 Angular 中，服务是单例的，我们在“root”中提供这个服务，因为我们希望所有需要访问 API 的人都可以使用它。正如你所看到的，我们以类似于 ASP.NET 核心的方式进行依赖注入，这里我们接收到一个 *HttpClient* ，它可以向一个 url 发出 http 请求，我们不必担心它是如何工作的，甚至不用担心如何创建它！...这就是我喜欢反转控制的原因。

让我们继续根据 id 获取一个配方:

```
 public async getById(id: number): Promise<RecipeDTO> {
    const res = await this.httpClient.get<any>(`${apiUrl}/recipe/${id}`).toPromise();
    return RecipeDTO.prepareFromJson(res);
  } 
```

同样，方法是异步的，因为它在继续之前等待 http 请求。正如你所看到的，我们没有返回一个 **RecipeDTO** ，而是返回一个 **Promise** ，Promise 是以打字稿(或 javascript)的方式告诉你，如果你调用 *getByid* ，你可以在未来某个时间得到一个结果。这就是为什么我们必须等待 http 请求的原因，我们强迫我们的异步代码看起来像同步代码(一行接一行地执行)。

让我们做一些更改，这样我们就可以在继续发布、上传和删除之前看到结果。

我们将为 CRUD 操作创建 recipes 模块。转到 src/app 文件夹并运行:

```
ng g module recipe
ng g component recipe
ng g service recipe
touch recipe-routing.module.ts 
```

我们做了什么？

1.  我们创建了一个新模块。是的，我们可以使用简单的组件，但这是保持应用程序模块化的好方法。
2.  我们创建了配方组件(。ts。html 和。scss)
3.  我们创建了一个食谱服务，像后端服务，它将帮助我们解决与食谱相关的复杂事情。
4.  我们创建了一个路由模块，每个角模块都需要一个路由模块来定义它的路由。(我们可以在 app-routing.module 中写所有的路由，但那并不牢靠)。

Angular CLI 为我们做了一些工作，例如，如果你打开 *recipe.module.ts* 你会看到 *RecipeComponent* 已经导入。

打开 *recipe-routing.module.ts* 并写入:

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { RecipeComponent } from './recipe.component';

const routes: Routes = [{
  path: '', component: RecipeComponent,
}
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class RecipeRoutingModule { } 
```

我们告诉角度路由，如果路由是空的，它应该加载 *RecipeComponent* 。现在我们需要在 *RecipeModule* 中导入这个 *RoutingModule* ，所以打开 *recipe.module.ts* 并在 *imports* 数组中写入 *RecipeRoutingModule* (不要忘记导入)。

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RecipeRoutingModule } from './recipe-routing.module';
import { RecipeComponent } from './recipe.component';

@NgModule({
  declarations: [
    RecipeComponent
  ],
  imports: [
    CommonModule,
    RecipeRoutingModule,
  ]
})
export class RecipeModule { } 
```

我们不希望我们的组件直接与后端服务交互，我们需要一个抽象层(想象这是数据库访问层，我们需要提供者)，这有许多原因，如果我们正在测试，也许我们可以为存根实现替换提供者。也许后端还没有准备好，我们将使用内存信息。

这就是我们之前创建 RecipeService 的原因。这项服务没有太多的线路，但很重要，所以在解释之前让我展示一下

```
import { Injectable } from '@angular/core';
import { RecipeBackendService } from '../shared/backend-services/recipe-backend.service';
import { Subject } from 'rxjs';
import { RecipeDTO } from '../shared/dtos/recipe.dto';

@Injectable({
  providedIn: 'root'
})
export class RecipeService {

  private recipes = new Subject<Array<RecipeDTO>>();

  get Recipes() { return this.recipes.asObservable(); }

  constructor(private recipeBackend: RecipeBackendService) { }

  public async getAllRecipes(): Promise<void> {
    const res = await this.recipeBackend.getAll();
    if (res) {
      this.recipes.next(res);
    }
  }

  public async getRecipe(id: number): Promise<RecipeDTO> {
    const res = await this.recipeBackend.getById(id);
    return res;
  }
} 
```

首先我们有一个食谱科目。

*大括号打开* Subject 就像一个进化的承诺，一个承诺只执行一次(用. then()或 await)并给出结果，但是 Subject 更像一个流，它可以发出值，任何知道 Subject 的人都可以接收这些值。这是一个非常基本和糟糕的定义，但初学者呢？标题是因为我总结了一些基础知识，这个社区里有很多关于承诺、异步、可观察性等的伟大文章。*大括号关闭*

然后，我们将主题的 getter 作为可观察对象，这是因为我们不希望该服务之外的任何人更改 recipes 数组(只接收)。

我们需要与 backed 通信，所以我们接收到一个 RecipeBackendService，你注意到了吗，我们并不关心这个服务是如何工作的？。如果您还记得，后端服务需要一个 httpClient，如果没有 IoC，我们将不得不做这样的事情:

```
const httpClient = new HttpClient(...);
const serviceBackend = new RecipeBackendService(httpClient); 
```

此外，在使用 serviceBackend 之前，谁知道 HttpClient 构造函数需要什么...你不得不承认 IoC 是一个伟大的工具。

最后我们有两种方法。getById 只能做一个中间件，但是 *getAllRecipes* 更有趣一点。首先，它从后端服务获取所有的食谱，然后它将数组传递给主题(这就是 *next* 所做的),这样每个订阅食谱的人都将获得新的数组。

现在我们有了我们的提供者，我们可以展示一些数据。首先让我们导入 bootstrap，有很多事情要做，但我喜欢这样:转到根项目文件夹中的 styles . scss(app 中的根 scss 文件)并粘贴

```
@import '~bootstrap/dist/css/bootstrap.min.css'; 
```

我们使用 httpClient 但是我们不告诉 Angular，所以我们必须这样做。像这种是全局服务，我们只需要声明一次，那么打开 app.module，将 **HttpClientModule** 和 **RouterModule** 添加到 imports。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';
import { RouterModule } from '@angular/router';
import { AppRoutingModule } from './app-routing.module';
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    RouterModule,
    AppRoutingModule,
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

我们必须导入 **RouterModule** ，因为我们将在 app 组件中执行一些路由操作。打开 app.component.html，将内容替换为

```
<header>
  <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
    <a class="navbar-brand" href="#">Recipes list</a>
  </nav>
</header>
<main role="main" class="container">
  <router-outlet></router-outlet>
</main> 
```

我们刚刚定义了一个基本的公共结构，包括一个导航栏和一个主容器(在以后的教程中，我们将对此进行重构)。在主容器中我们放了一个**，这里 Angular 将加载定义为 **AppModule** 的子模块。每个模块都可以有一个(mamushka 风格的),在那里它可以加载自己的子模块。**

 **现在我们必须修改 *app-routing.module.ts* 以表明 Recipes 是一个子路由。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
  { path: '', loadChildren: 'src/app/recipe/recipe.module#RecipeModule' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

这是根模块，所以我们用*导入路由。forRoot* (因为 Angular 先生希望这样)。我们告诉如果路线是空的，angular 应该加载 **RecipeModule** 。这是一个惰性导入，这意味着模块*直到路线导航需要时才被加载*，这对于性能来说非常酷，一些项目可能有数百个模块，想象一下，如果你必须等待所有屏幕被加载，而你只想导航到你的个人资料模块(我在和你说 php)。

我们非常接近看到一些结果。现在打开 *recipe.component.ts* 并写入

```
import { Observable } from 'rxjs';
import { RecipeService } from './recipe.service';
import { RecipeDTO } from '../shared/dtos/recipe.dto';

@Component({
  selector: 'app-recipe',
  templateUrl: './recipe.component.html',
  styleUrls: ['./recipe.component.sass']
})
export class RecipeComponent implements OnInit {

  public recipes$: Observable<Array<RecipeDTO>>;

  constructor(private recipeService: RecipeService) {
    this.recipes$ = this.recipeService.Recipes;
  }

  ngOnInit() {
    this.recipeService.getAllRecipes();
  }

} 
```

好吧，发生什么事了？。首先我们声明一个公共可观测的，还记得可观测的吗？如果你不这样做，就像我们可以通过订阅来接收信息的数据流。Recipes 是公共的，因为我们将在 html 中使用它，如果我们在 html 中使用一个在组件中不是公共的属性(如果我们在生产模式下编译)，typescript 编译器将会抱怨。

然后，在构造函数中，我们得到在 **RecipeService** 中声明的配方可观察值(感谢 DI，我们不知道这是如何工作的，我们只是得到了它)。之后，我们调用 *getAllRecipes* 来更新数据流。

最后一步是显示数据。将*recipe.component.html*修改为:

```
<ng-container *ngIf="recipes$ | async as recipes; else loadingTemplate">
  <ng-container *ngIf="recipes.length > 0; else noDataTemplate">
    <ul class="list-group">
      <li class="list-group-item" *ngFor="let recipe of recipes">
        {{recipe.title}}
      </li>
    </ul>
  </ng-container>
  <ng-template #noDataTemplate>No data.</ng-template>
</ng-container>
<ng-template #loadingTemplate>Loading...</ng-template> 
```

这里发生了许多事情。首先，我们使用 *ng-container* 来检查配方是否存在以及它是否有数据。关于 *ng-container* 的伟大之处在于它不创建任何 html 标签。我们本来可以使用 div，但并不是所有有效的东西都是正确的。

ng 模板就像 html 变量，所以如果有一些食谱，我们显示它们，但是如果没有，我们显示一个*无数据*消息。

最后，我们将看到一些食物！，首先转到后端项目根文件夹并运行:

```
dotnet run 
```

现在运行 Angular 项目，在根文件夹中:

```
npm start 
```

去本地主机:4200 和 TADA！:
[![](img/3f704f14004fe18787595e756687229b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4AW5gsJr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7detnbpbmxv3lgxhjfhl.png)

好吧，今天到此为止。为了发布文章，我们应该创建视图模型，这意味着重构，这意味着本教程系列的下一篇文章。**