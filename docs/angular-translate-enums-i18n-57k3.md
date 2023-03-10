# Angular - Translate Enums (i18n)

> 原文：<https://dev.to/constjs/angular-translate-enums-i18n-57k3>

内置的 Angular translation engine(到目前为止)只支持模板内的翻译，所以你可能会认为不可能像 enums 一样翻译部分 TypeScript。

事实上，如果你坚持练习，这是很容易的。

## 模型来自服务器

为了使事情更容易推理，让我们有一个待办事项应用程序:)

```
interface TodoItem {
  name: string;
  state: TodoState;
}

enum TodoState {
  TODO = 'TODO',
  IN_PROGRESS = 'IN_PROGRESS',
  DONE = 'DONE',
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
@Component({
  selector: 'todo-list',
  template: `
    <ul>
      <li *ngFor="let item of items">
      {{ item.name }} ({{ item.state }})
      </li>
    </ul>
  `,
})
export class TodoList {

  @Input()
  items: TodoItem[];
} 
```

Enter fullscreen mode Exit fullscreen mode

那么问题是什么呢？

1.  `{{ items.state }}`将产生生成的枚举值(0，1，2...或' TODO '，' IN_PROGRESS '...)
2.  我们需要将枚举值转换为字符串，但是这必须在模板中，而不是在 TypeScript 中

### 坏榜样

通常我们倾向于用 switch-case 来创建方法，这很不幸，因为 Angular i18n 不知道这些字符串，所以在翻译过程中它不会接触到它们。

```
// Don't do it
getStateMessage(state: TodoState) {
  switch(state) {
    case TodoState.TODO:
      return 'not started';
    case TodoState.IN_PROGRES:
      return 'started';
    case TodoState.DONE:
      return 'finished';
    default:
      return 'unknown';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使其可译？

只有一条规则可以遵循:

> UI 中可见的每个字符串都必须放在模板中

通常在我们的团队中，对于复杂的字符串计算(枚举，或者一些文本逻辑)，我们创建只负责翻译的新组件。

我们在应用程序中广泛使用它，在屏幕逻辑和文本逻辑之间做了明确的区分。

### 解决方案 1

```
@Component({
  selector: 'todo-state-i18n',
  template: `
  <ng-container [ngSwitch]="key">
    <ng-container i18n *ngSwitchCase="todoState.TODO">not started</ng-container>
    <ng-container i18n *ngSwitchCase="todoState.IN_PROGRESS">started</ng-container>
    <ng-container i18n *ngSwitchCase="todoState.DONE">finished</ng-container>
    <ng-container i18n *ngSwitchDefault>not defined</ng-container>
  </ng-container>
  `,
})
export class TodoStateI18n {

  // enum has to be accessed through class field
  todoState = TodoState;

  @Input()
  key: TodoState;
} 
```

Enter fullscreen mode Exit fullscreen mode

以及最终用途:

```
@Component({
  selector: 'todo-list',
  template: `
    <ul>
      <li *ngFor="let item of items">
      {{ item.name }} (<todo-state-i18n key="item.state"></todo-state-i18n>)
      </li>
    </ul>
  `,
})
export class TodoList {

  @Input()
  items: TodoItem[];
} 
```

Enter fullscreen mode Exit fullscreen mode

*   这只适用于常规枚举，`const enum`不能在模板中使用(至少不能开箱即用)
*   我们很高兴地将这种做法不仅用于枚举，还用于字符串操作。
*   当添加新的枚举值时，您仍然需要记住更新模板(例如`TodoState.BLOCKED`)

### 解决方案 2 - ICU 消息

```
@Component({
  selector: 'todo-state-i18n',
  template: `
  <ng-container i18n>
    {key, select,
      TODO {not started}
      IN_PROGRESS {started}
      DONE {finished}
    }
  </ng-container>
  `,
})
export class TodoStateI18n {

  @Input()
  key: TodoState;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   使用常量枚举
*   对字符串枚举特别有用
*   更简单的方法，但也支持 HTML 元素，例如`TODO {<span>not</span> started}`
*   为了安全起见，您需要编写检查枚举值的单元测试