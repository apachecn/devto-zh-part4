# 测试 redux redux-利用选择器

> 原文：<https://dev.to/ph1/testing-redux-reducers-leveraging-selectors-2p76>

这篇文章是一个系列的一部分，涵盖了 redux reducers 的测试技术。在本系列的第一部分中，我们看到最好将 reducers 和 actions 作为一个集成的、内聚的单元一起测试。在这篇文章中，我们将进一步阐述这个想法，看看当我们将[选择器](https://medium.com/@matthew.holman/what-is-a-redux-selector-a517acee1fe8)引入我们的减速器测试策略时会发生什么。具体来说，我们将通过使用选择器来验证我们对 redux 状态的期望，来看看我们是否能够使我们的 reducer 单元测试更具表现力。我们还将简要地看一下反向使用的*减速器*，以使我们的*选择器单元测试*更有表现力。

## 完成一些待办事项

让我们从查看一个示例 reducer 测试开始，看看我们是否可以改进它。和本系列的其他文章一样，我们将测试一个管理待办事项列表的缩减器。具体来说，在这个例子中，我们测试我们如何跟踪哪些待办事项已经完成:

```
import reducer, {addTodo,completeTodo} from './todos';

describe('todos duck', () => {
  describe('completing todos', () => {
    it('initializes new todos as not completed', () => {
      const todoId = 'todo-a';

      const initialState = undefined;
      const nextState = reducer(
        initialState,
        addTodo({id:todoId,text:'blah'})
      );

      expect(nextState).toEqual([
        {
          id: todoId,
          text: 'blah',
          completed: false
        }
      ]);
    });

    it('allows us to mark an existing todo as completed', () => {
      const todoId = 'todo-a';

      let state = undefined;
      state = reducer(
        state,
        addTodo({id:todoId,text:'blah'})
      );
      state = reducer(
        state,
        completeTodo({id:todoId})
      );

      expect(state).toEqual([
        {
          id: todoId,
          text: 'blah',
          completed: true
        }
      ]);
    });
  });
}); 
```

这些测试完成了工作，可读性很好，尽管它们有点冗长。

读者在这些测试结束时看到期望，可能需要一点时间来理解期望的哪一部分与这些特定的测试相关。这里`text`的值重要吗？答案是否定的。在这个测试中，我们根本不关心那个领域。然而，由于我们使用`toEqual`来验证我们的期望，我们必须指定每个字段的值，尽管我们实际上只关心`completed`字段。换句话说，我们的测试被过度指定了。

## 超过规定的测试为脆性测试

过度指定的测试不仅会分散注意力，而且也更加脆弱。我们的期望是明确地检查我们状态的每一个方面，这意味着如果我们对那个状态形状做*任何*改变，我们将不得不接触一堆与改变无关的测试。假设我们决定通过添加一个`createdAt`时间戳来跟踪每个 todo 的创建时间。这个新字段将打破这些测试中的过度指定的期望，我们将不得不回来更新每一个。

有几个选项可以解决这个问题。我们可以编写多个期望来检查我们关心的单个字段，而不是检查整个状态——但这很快就会变得难以处理。我们也可以考虑使用 Jest 的匹配器
的一些[更高级的特性](https://app.getpocket.com/read/1556304876)

```
import reducer, {addTodo,completeTodo} from './todos';

describe('todos duck', () => {
  describe('completing todos', () => {
    it('initializes new todos as not completed', () => {
      const todoId = 'todo-a';

      const initialState = undefined;
      const nextState = reducer(
        initialState,
        addTodo({id:todoId,text:'blah'})
      );

      expect(nextState).toEqual([
        expect.objectContaining({     // 👈 fancier expectatations
          id: todoId,
          completed: false
        })
      ]);
    });

    it('allows us to mark an existing todo as completed', () => {
      const todoId = 'todo-a';

      let state = undefined;
      state = reducer(
        state,
        addTodo({id:todoId,text:'blah'})
      );
      state = reducer(
        state,
        completeTodo({id:todoId})
      );

      expect(state).toEqual([
        expect.objectContaining({     // 👈 fancier expectatations
          id: todoId,
          completed: true
        })
      ]);
    });
  });
}); 
```

这可能更好一点。我们不再被验证`text`字段的值的期望所分散，当它与手头的测试不相关时。更重要的是，这些测试对不相关的变化更有弹性——例如，如果我们将那个`createdAt`时间戳添加到我们的状态中，我们就不需要接触这些测试。

然而，我们也使我们的期望陈述变得更加复杂，并且引入了一些阅读代码的人可能不熟悉的特性。

## 移动到选择器

让我们试着采取不同的策略。从概念上讲，我们在这些测试中试图做的是在某些操作之后验证 to do 的完整性。如果我们正在编写想要查看 todo 完整性的生产代码，我们不会直接在 redux 状态中探索——我们会使用选择器来提取信息。例如，这里有一个`isTodoCompleted`选择器:

```
export function isTodoCompleted(state,todoId){
  const targetTodo = state.find( (todo) => todo.id === todoId );
  return targetTodo.completed;
}; 
```

如果我们开始在测试代码中使用这样的选择器，就像在生产代码中一样，会是什么样子呢？

```
import reducer, {addTodo,completeTodo} from './todos';
import {isTodoCompleted} from './selectors';

describe('todos duck', () => {
  describe('completing todos', () => {
    it('initializes new todos as not completed', () => {
      const todoId = 'todo-a';

      const initialState = undefined;
      const nextState = reducer(
        initialState,
        addTodo({id:todoId,text:'blah'})
      );

      // 👇 using a selector to query our state
      expect(isTodoCompleted(nextState,todoId)).toBe(false);
    });

    it('allows us to mark an existing todo as completed', () => {
      const todoId = 'todo-a';

      let state = undefined;
      state = reducer(
        state,
        addTodo({id:todoId,text:'blah'})
      );
      state = reducer(
        state,
        completeTodo({id:todoId})
      );

      // 👇 using a selector to query our state
      expect(isTodoCompleted(state,todoId)).toBe(true);
    });
  });
}); 
```

现在我们有进展了！这些期望更容易理解，更重要的是，它们对我们产品代码的变化更有弹性。如果我们在生产代码中改变状态的形状，我们就必须更新任何相关的选择器，这意味着使用这些选择器的测试将“免费”获得更新。不需要接触任何一行测试代码，除非 redux 代码的外部行为有实质性的改变。即使我们要对测试中的行为的内部实现进行巨大的改变，这也是适用的。例如，我们可以将存储 todo 完成度的地方移到 redux 状态的一个完全不同的区域，而不需要接触任何一个测试。这是一个巨大的胜利，在我看来，这也是转向一种更加集成的方法来测试 redux redox 的最大动力。

## 测试选择器

我们刚刚看到了使用选择器来验证缩减器产生的状态，但是我们也可以反过来使用缩减器来设置状态，然后用它来验证选择器的行为。

这里我们有一个新的选择器，`getTodoCounts`，它告诉我们有多少待办事项是完整的还是不完整的:

```
export function getTodoCounts(state){
  const completedTodos = state.filter( (todo) => todo.completed );

  const total = state.length;
  const complete = completedTodos.length;
  const incomplete = total - complete;

  return {
    total,
    complete,
    incomplete
  };
} 
```

为了测试这个选择器，我们想让 redux 状态变成一个形状，模拟列表中有几个 todos，其中一个已经完成。我们可以在测试中手工创建模拟状态，但是这种方法会有许多与 reducer 测试中手工验证状态相同的缺点——它会很冗长，难以阅读，而且会使测试实现变得非常脆弱——对 redux 状态形状的更改需要对这些测试进行更改。因此，与其手工创建这个状态，不如使用我们的 reducer:

```
import reducer, {addTodo,completeTodo} from './todos';
import {getTodoCounts} from './selectors';

describe('todos selectors', () => {
  it('correctly counts todos in various states', () => {
    let state = undefined;
    state = reducer( state, addTodo({id:'todo-a',text:'blah'}) );
    state = reducer( state, addTodo({id:'todo-b',text:'blah'}) );
    state = reducer( state, addTodo({id:'todo-c',text:'blah'}) );
    state = reducer( state, completeTodo({id:'todo-b'}) );

    expect(getTodoCounts(state)).toEqual({
      total: 3,
      complete: 1,
      incomplete: 2
    });
  });
}); 
```

这应该是不言自明的。从一个空的初始状态开始，我们添加三个 todos，完成一个，然后验证来自我们的`getTodoCounts`的计数与刚刚发生的相匹配。这种类型的选择器测试与我们在 reducer 测试中看到的有着相同的好处——测试更具表现力，与我们如何建模 todo 列表状态的内部耦合更少。

## 我们不能使用快照测试吗？

有些人可能想知道 Jest 的[快照测试](https://jestjs.io/docs/en/snapshot-testing)特性是验证 reducer 操作创建的状态的另一种方法。这里有一个例子，我们如何重写我们之前看到的 reducer 测试来使用快照:

```
import reducer, {addTodo,completeTodo} from './todos';

describe('todos duck', () => {
  describe('completing todos', () => {
    it('initializes new todos as not completed', () => {
      const todoId = 'todo-a';

      const initialState = undefined;
      const nextState = reducer(
        initialState,
        addTodo({id:todoId,text:'blah'})
      );

      expect(nextState).toMatchSnapshot();
    });

    it('allows us to mark an existing todo as completed', () => {
      const todoId = 'todo-a';

      let state = undefined;
      state = reducer(
        state,
        addTodo({id:todoId,text:'blah'})
      );
      state = reducer(
        state,
        completeTodo({id:todoId})
      );

      expect(state).toMatchSnapshot();
    });
  });
}); 
```

我不喜欢这种方法，主要有两个原因:这些测试不再传达预期的行为，而且它们*非常*脆弱。虽然这些测试确实很简洁，但我不认为它们很有表现力。看看这个测试，我们能告诉这些`addTodo`和`completeTodo`动作应该对我们的 redux 状态做什么吗？我们可以*根据测试的描述和动作本身的名称来猜测*，但是测试本身除了“它应该以和上次一样的方式工作”之外不传达任何信息。这个问题的另一面是，如果*任何事情*改变了国家的形态，这些测试将开始失败。这些测试和我们最初使用`toEqual`的测试一样脆弱。虽然使用`jest --updateSnapshot`很容易重新拍摄测试快照，但这实际上只是对潜在问题——过度指定的测试——的除臭。

## 使用选择器的弊端

我们已经看到，当我们在 reducer 测试中使用选择器时，我们得到的测试更有表现力，而且不那么脆弱。但是有哪些缺点呢？一个担心是，随着我们的测试引入更多的产品代码，它们变得不那么集中。如果我们在使用选择器的缩减器测试中失败，我们最初不知道问题是出在我们测试的缩减器上，还是出在我们用来帮助我们测试的选择器上。我们得做些调查才能弄清楚。

这是您在集中的、孤立的测试和更广泛的、集成的测试之间看到的一组更一般的权衡的例子:

| 更加孤立 | 更加一体化 |
| --- | --- |
| *聚焦*:更容易找到测试失败的原因 | *更宽泛的*:测试失败可能有几个原因 |
| 人为的:倾向于依赖测试替身，这些替身可能不能反映现实 | *现实*:使用真实的依赖关系，测试更接近生产场景 |
| 脆弱的:依赖关系代码的改变更有可能需要测试的改变 | 柔软的:依赖关系的改变不太可能需要测试代码的改变 |

在这些权衡中工作的最佳方式是创建一个[混合测试组合](https://martinfowler.com/bliki/TestPyramid.html)，其中各种测试风格弥补了其他风格的缺点。

## 超越鸭子

在本文的开始，我提到了另一篇文章，它主张在我们的 redux 测试中包含 redux 动作，并展示了这是如何揭示 redux 代码中一个更大的组织单元的形状的——鸭子[。我指出了动作创建者是如何为 reducer 形成一种公共接口的，我们应该把鸭子作为一个集成单元来测试。](https://github.com/erikras/ducks-modular-redux)

我现在将把这一论点推进一步。从我们对测试策略的探索中，我清楚地看到了鸭子公共接口缺失的后半部分的选择器。

[![A duck module, where selectors provide a public query interface and actions a public command interface](img/9d4239d1bf5285f52c619f54f9d3f2d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a1pyoqBZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gyvnjhemflpwumqkf4sw.jpg)

这种结构很像是一种 [CQRS(命令查询责任分离)](https://martinfowler.com/bliki/CQRS.html)架构。选择器是*查询*，用于检查我们的应用程序的当前状态，而动作是*命令*，用于影响到那个状态的变化 <sup id="fnref1">[1](#fn1)</sup> 。

虽然 duck 模块的最初提议没有提到选择器，但我认为将 duck 模块的范围扩展到包括选择器是有意义的。我先从[不是](https://www.freecodecamp.org/news/scaling-your-redux-app-with-ducks-6115955638be/)来指出这一点。

## 总结我们的选项

在本文中，我们使用了四种不同的方法来验证 redux 状态的预期。

我们使用 Jest 的`toEqual`匹配器进行精确的等式匹配:

```
expect(state).toEqual([
  {
    id: todoId,
    text: 'blah',
    completed: true
  }
]); 
```

我们使用 Jest 的`objectContaining`匹配器进行更灵活的部分匹配:

```
expect(state).toEqual([
  expect.objectContaining({
    id: todoId,
    completed: true
  })
]); 
```

我们使用了选择器:

```
expect(isTodoCompleted(state,todoId)).toBe(true); 
```

最后，我们试用了 Jest 的快照测试工具:

```
expect(state).toMatchSnapshot(); 
```

将这些选项放在一起看，选择器是最具表现力的选项就更清楚了。我们还看到选择器使我们的测试更加灵活，对产品代码重构更有弹性。

下次编写 reducer 测试时，我希望您已经考虑过使用选择器来验证 redux 状态。并且[让我知道](https://twitter.com/ph1)它是如何工作的！请在这里或 twitter 上关注我，当我发布本系列的第 3 篇文章时，您会得到通知，这篇文章关注的是如何为 reducers 编写更高级的有状态单元测试。

* * *

1.  我发现非常有趣的是，redux，它紧跟函数式编程风格，最终对[命令/查询分离](https://martinfowler.com/bliki/CommandQuerySeparation.html)进行建模，这在很大程度上来自于软件的面向对象视角。我的朋友兼前同事 [Andrew Kiellor](https://twitter.com/akiellor) 向我指出了这一点。 [↩](#fnref1)