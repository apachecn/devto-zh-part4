# Angular:如何轻松显示负载指示器

> 原文：<https://dev.to/johncarroll/angular-how-to-easily-display-loading-indicators-4359>

Angular 应用程序的一个常见任务是在页面导航期间显示加载指示器，或者在向服务器发送数据时显示加载指示器，或者在发生`*something*`时显示加载指示器。

我最近[读了 Nils Mehlhorn](https://dev.to/angular/loading-indication-in-angular-52b6) 写的一篇很棒的文章，讨论了你可以用来显示/管理负载指标的各种策略。这是一个很好的读物，但是我不认为讨论的任何选项都不如使用我为这个任务制作的小 [IsLoadingService](https://gitlab.com/service-work/is-loading) 好。请允许我用一些互相依赖的例子来演示(一些例子摘自 Nils 的帖子)。

## 表示页面正在加载

让我们从页面加载开始。很多(大部分？Angular 应用程序将利用 Angular 路由器来允许在应用程序的各个部分之间导航。我们希望在导航挂起时显示一个装载指示器。

对于我们的加载指示器示例，我们将使用来自`@angular/material`库的 [MatProgressBar 组件](https://material.angular.io/components/progress-bar/overview#indeterminate)，并将其显示在页面顶部。

```
@Component({
  selector: 'app-root',
  template: `
    <mat-progress-bar
      *ngIf="isLoading | async"
      mode="indeterminate"
      color="warn"
      style="position: absolute; top: 0; z-index: 5000;"
    >
    </mat-progress-bar>

    <router-outlet></router-outlet>
  `,
})
export class AppComponent {
  isLoading: Observable<boolean>;

  constructor(
    private isLoadingService: IsLoadingService,
    private router: Router,
  ) {}

  ngOnInit() {
    this.isLoading = this.isLoadingService.isLoading$();

    this.router.events
      .pipe(
        filter(
          event =>
            event instanceof NavigationStart ||
            event instanceof NavigationEnd ||
            event instanceof NavigationCancel ||
            event instanceof NavigationError,
        ),
      )
      .subscribe(event => {
        // If it's the start of navigation, `add()` a loading indicator
        if (event instanceof NavigationStart) {
          this.isLoadingService.add();
          return;
        }

        // Else navigation has ended, so `remove()` a loading indicator
        this.isLoadingService.remove();
      });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用`IsLoadingService`来获取我们的加载状态作为一个可观察值，保存在类属性`isLoading`中，并在我们的根组件的模板中订阅它。当加载为`false`时，加载指示灯将消失。

[Angular IsLoadingService](https://gitlab.com/service-work/is-loading) 是 Angular 的一个小服务(小于 1kb minzipped ),帮助我们跟踪“东西”是否正在加载。我们可以通过`IsLoadingService#isLoading$()`订阅加载状态，它返回一个`Observable<boolean>`值，指示状态是否正在加载。

*   为了表明某个东西已经开始加载，我们可以调用`IsLoadingService#add()`。
*   当某个东西停止加载时，我们可以调用`IsLoadingService#remove()`。
*   只要有一个或多个东西还在加载，IsLoadingService 就会从`isLoading$()`发出`true`。

因为我们希望在路由器导航期间显示一个加载栏，所以我们订阅了 angular `Router`事件。当发出一个`NavigationStart`事件时，我们说装载已经开始(通过`add()`)。当`NavigationEnd || NavigationCancel ||NavigationError`事件发出时，我们说装载已经停止(通过`remove()`)。

有了这段代码，我们的应用程序现在将在页面顶部显示一个加载栏，同时在路线之间导航。

*   注意:如果我们用一个`Subscription`、`Promise`或`Observable`值调用`IsLoadingService#add()`，我们就不需要调用`remove()`。
    *   当通过一个`Subscription`或`Promise`时，`add()`会自动标记`Subcription`或`Promise`完成后已经停止加载。
    *   在有`Observable`值的情况下，`add()`将从可观测值中`take(1)`并订阅它，注意到当它发出下一个值时`Observable`的加载已经停止。
    *   以下是更多相关信息...

## 等待数据

已经完成了在路由器导航期间添加“页面加载”指示器的任务，现在我们想在从服务中异步获取用户列表时显示一个加载指示器。我们决定触发刚才设置的进度条指示器来指示页面正在加载。

```
@Component({
  selector: 'user-component'
  template: `
    <list-component>
      <profile-component *ngFor="let user of users | async" [user]='user'>
      </profile-component>
    </list-component>
  `
})
export class UserComponent implements OnInit  {
  users: Observable<User[]>;

  constructor(
    private userService: UserService,
    private isLoadingService: IsLoadingService,
  ) {}

  ngOnInit() {
    this.users =
      this.isLoadingService.add( this.userService.getAll() );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，在这个例子中，我们不需要管理额外的订阅或变量。

当你用一个`Observable`(或`Promise`或`Subscription`)参数调用`IsLoadingService#add()`时，那个参数被返回。

所以这段代码...

```
this.users = this.isLoadingService.add(this.userService.getAll()); 
```

Enter fullscreen mode Exit fullscreen mode

就`this.users`而言，与此代码相同。

```
this.users = this.userService.getAll(); 
```

Enter fullscreen mode Exit fullscreen mode

另外(如前所述)，当向`IsLoadingService#add()`传递一个`Observable`值时，IsLoadingService 将从该值中`take(1)`并订阅结果。当这个组件初始化后，`this.isLoadingService.add( this.userService.getAll() )`将被调用，触发我们之前设置的“页面加载”指示器。当从`this.userService.getAll()`返回的可观察对象第一次发出时，IsLoadingService 会知道这个可观察对象已经停止加载，并适时更新“页面加载”指示器。

如果`this.userService.getAll()`返回一个承诺(或订阅)，我们也可以将它传递给`this.isLoadingService.add()`并获得类似的结果。

## 提交表单

接下来，我们想让我们的用户通过提交用户创建表单来创建一个新的`User`。当我们这样做时，我们希望禁用表单的“提交”按钮，并将其样式化以指示表单处于待定状态。我们还决定，在这种情况下，我们*不想*像以前一样显示相同的“页面加载”进度条。

完成这项任务的一种方法如下...

```
@Component({
  selector: 'user-component'
  template: `
    <form [formGroup]='userForm' novalidate>
      <mat-form-field>
        <mat-label> Your name </mat-label>
        <input matInput formControlName='name' required />
      </mat-form-field>

      <button
        mat-button
        color='primary'
        [disabled]='userFormIsPending | async'
        (click)='submit()'
      >
        Submit
      </button>
    </form>
  `,
  styles: [`
    .mat-button[disabled] {
      // button pending styles...
    }
  `]
})
export class UserComponent implements OnInit  {
  userForm: FormGroup;
  userFormIsPending: Observable<boolean>;

  constructor(
    private userService: UserService,
    private isLoadingService: IsLoadingService,
    private fb: FormBuilder,
    private router: Router,
  ) {}

  ngOnInit() {
    this.userForm = this.fb.group({
      name: ['', Validators.required],
    });

    this.userFormIsPending =
      this.isLoadingService.isLoading$({ key: 'create-user' });
  }

  async submit() {
    if (this.userForm.invalid) return;

    const response = await this.isLoadingService.add(
      this.usersService.createUser(
        this.userForm.value
      ).toPromise(),
      { key: 'create-user' }
    )

    if (response.success) {
      this.router.navigate(['user', response.data.userId, 'profile']);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些新概念。首先，我们不是订阅“默认”加载状态，而是通过将`key`选项传递给`IsLoadingService#isLoading$()`来订阅“创建用户”加载状态。在这种情况下，`userFormIsPending`将忽略页面加载指示器状态。它只关心加载使用`add({key: 'create-user'})`添加的东西。

接下来，当我们的`userForm`被提交时，我们将表单值传递给`UsersService#createUser()`，后者返回一个可观察值。我们将可观察的转化为`Promise`，而`await`就是结果。我们也通过`key: 'create-user'`将这一承诺传递给`IsLoadingService#add()`。

如果我们的突变成功，我们将导航到新用户的个人资料页面。

在组件模板中，我们订阅了`userFormIsPending`，当`submit()`待定时,“提交”按钮会自动禁用。

这个例子非常简洁，但是它仍然有这个不必要的`userFormIsPending`属性。我们可以改善事情...

### 用 [IsLoadingPipe](https://gitlab.com/service-work/is-loading#isloadingpipe) 简化表单提交

我们可以简化前面例子的一个快速方法是在`IsLoadingService`旁边使用可选的`IsLoadingPipe`。`IsLoadingPipe`简化了在组件模板中订阅加载状态的任务。

IsLoadingPipe 是一个角形管道，它接收一个`key`参数，并返回该键的一个`isLoading$({key})`可观察值。重要的是，它很好地配合了 Angular 的变化检测。因为 IsLoadingPipe 返回一个可观察对象，所以您应该将它与 Anguilar 内置的`AsyncPipe`一起使用。

```
@Component({
  selector: 'user-component'
  template: `
    <form [formGroup]='userForm' novalidate>
      <mat-form-field>
        <mat-label> Your name </mat-label>
        <input matInput formControlName='name' required />
      </mat-form-field>

      <button
        mat-button
        color='primary'
        [disabled]='"create-user" | swIsLoading | async' <!-- change is here -->
        (click)='submit()'
      >
        Submit
      </button>
    </form>
  `,
  styles: [`
    .mat-button[disabled] {
      // button pending styles...
    }
  `]
})
export class UserComponent implements OnInit  {
  userForm: FormGroup;

  constructor(
    private userService: UserService,
    private isLoadingService: IsLoadingService,
    private fb: FormBuilder,
    private router: Router,
  ) {}

  ngOnInit() {
    this.userForm = this.fb.group({
      name: ['', Validators.required],
    });
  }

  async submit() {
    if (this.userForm.invalid) return;

    const response = await this.isLoadingService.add(
      this.usersService.createUser(
        this.userForm.value
      ).toPromise(),
      { key: 'create-user' }
    )

    if (response.success) {
      this.router.navigate(['user', response.data.userId, 'profile']);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意，我们不再需要组件上的`userFormIsPending`属性。*

然而，我们可以进一步改进这个例子。除了简单地禁用用户表单的“提交”按钮之外，我们还想在加载过程中给按钮添加一个“正在加载”的 css 类。我们还想在按钮加载时添加一个动画 css 微调器。

### 用 [IsLoadingDirective](https://gitlab.com/service-work/is-loading#isloadingdirective) 改进表单提交

使用可选的`IsLoadingDirective`和`IsLoadingService`，我们可以改进前面的例子。`IsLoadingDirective`允许我们基于加载状态轻松地设计(或者禁用)元素。

```
@Component({
  selector: 'user-component'
  template: `
    <form [formGroup]='userForm' novalidate>
      <mat-form-field>
        <mat-label> Your name </mat-label>
        <input matInput formControlName='name' required />
      </mat-form-field>

      <button
        mat-button
        color='primary'
        swIsLoading='create-user' <!-- change is here -->
        (click)='submit()'
      >
        Submit
      </button>
    </form>
  `,
  styles: [`
    .sw-is-loading {
      // button styles...
    }

    .sw-is-loading .sw-is-loading-spinner {
      // spinner styles
    }
  `]
})
export class UserComponent implements OnInit  {
  userForm: FormGroup;

  constructor(
    private userService: UserService,
    private isLoadingService: IsLoadingService,
    private fb: FormBuilder,
    private router: Router,
  ) {}

  ngOnInit() {
    this.userForm = this.fb.group({
      name: ['', Validators.required],
    });
  }

  async submit() {
    if (this.userForm.invalid) return;

    const response = await this.isLoadingService.add(
      this.usersService.createUser(
        this.userForm.value
      ).toPromise(),
      { key: 'create-user' }
    )

    if (response.success) {
      this.router.navigate(['user', response.data.userId, 'profile']);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们通过`swIsLoading='create-user'`将`IsLoadingDirective`应用于我们的“提交”按钮。我们将`'create-user'`键传递给指令，告诉它订阅“创建用户”加载状态。

当“创建用户”状态正在加载时，IsLoadingDirective 将自动禁用“提交”按钮，并向该按钮添加一个`sw-is-loading` css 类。当加载停止时，按钮将被激活，并且`sw-is-loading` css 类将被删除。

因为这是一个按钮/锚元素，IsLoadingDirective 还将向“提交”按钮添加一个`sw-is-loading-spinner`子元素。

```
<sw-is-loading-spinner class="sw-is-loading-spinner"></sw-is-loading-spinner> 
```

Enter fullscreen mode Exit fullscreen mode

使用 css，您可以添加一个微调动画([示例，这里是](https://tobiasahlin.com/spinkit/))到这个元素中，以便按钮在加载期间显示一个挂起的动画。所有这些选项都是可配置的。例如，如果您不希望在按钮加载时添加一个 spinner 元素，您可以通过注入新的缺省值进行全局配置，或者通过`swIsLoadingSpinner=false`进行本地配置。

### 一次触发多个加载指示灯

作为上面的最后一个变化，假设我们想在表单提交期间触发页面加载指示器和“创建用户”指示器。您可以通过将一组键传递给`IsLoadingService#add()`来实现这一点。

在这种情况下，您可以用...

```
export class UserComponent implements OnInit {
  async submit() {
    if (this.userForm.invalid) return;

    const response = await this.isLoadingService.add(
      this.usersService.createUser(this.userForm.value).toPromise(),
      { key: ['default', 'create-user'] }, // <-- change here
    );

    if (response.success) {
      this.router.navigate(['user', response.data.userId, 'profile']);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不带键参数调用`IsLoadingService#isLoading$()`和用`isLoading$({key: 'default'})`调用它是一样的，所以你可以通过传递`"default"`作为键来触发“默认”加载状态。

## 结论

IsLoadingService 在过去一年左右的时间里被反复创建。在这一点上，我发现它极大地简化了在我的应用程序中加载指示器的管理。

### 可以在:【https://gitlab.com/service-work/is-loading】T2 查看