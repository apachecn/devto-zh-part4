# 测试 redux 减速器-故事测试

> 原文：<https://dev.to/ph1/testing-redux-reducers-story-tests-5bi8>

这篇文章是一个系列的一部分，涵盖了 redux reducers 的测试技术。

在本系列的第一篇文章中，我们发现最好将 reducers 和 actions 作为一个集成的、内聚的单元一起测试——[鸭](https://github.com/erikras/ducks-modular-redux)。在[系列的下一部分](https://dev.to/ph1/testing-redux-reducers-leveraging-selectors-2p76)中，我们将选择器纳入了集成单元的范围。我们还研究了如何在选择器测试中模拟不同的场景，方法是使用一个缩减器将 redux 状态转换成适当的形状。然而，我们进行设置的方式有点笨拙。

在本帖中，我们将探索更简洁、更具表现力的技术来创建这种有价值的 reducer 单元测试——这些测试通过移动存储经过相应的状态转换序列来验证 reducer 在响应一系列动作时的高级行为。)

## 测试有状态行为

我们将回到我们在本系列中一直使用的同一个待办事项列表缩减器。我们将验证这个缩减器是否支持添加具有相同文本的重复 todo 项。下面是测试这种行为的第一步:

```
import {getTodoCounts} from './selectors';
import reducer, {addTodo} from './duck';

describe('todos duck', () => {
  it('supports multiple identical items', () => {
    let state = undefined;

    state = reducer(
      state,
      addTodo('identical todo')
    );

    expect(getTodoCounts(state).total).toEqual(1);

    state = reducer(
      state,
      addTodo('identical todo')
    );

    expect(getTodoCounts(state).total).toEqual(2);
  });
}); 
```

我们从初始状态开始(通常在 redux 中表示为`undefined`)。然后我们使用 reducer 将一个`addTodo`动作应用到初始状态，生成一个新的包含一个项目的第二个状态。我们确认第二个状态看起来是正确的(利用选择器，[，如前所述](https://dev.to/ph1/testing-redux-reducers-leveraging-selectors-2p76))。然后我们采取第二种状态，再次使用减速器*进行第二次`addTodo`动作。这将生成第三个*状态，它应该包含两个相同的项目，然后我们将对其进行验证。**

 *我把这些类型的有状态单元测试称为*故事测试*。每个测试讲述了一段时间内发生的一系列动作的故事，描述了这些动作在被 reducer 处理时如何改变存储的状态。我发现故事测试是一种非常简洁、富有表现力的方法，通过它的公共 API <sup id="fnref1">[1](#fn1)</sup> 来验证一个 reducer 的核心行为。

你可能已经注意到，我们上面的测试并没有遵循单元测试通常[推荐的](https://xp123.com/articles/3a-arrange-act-assert/)的排列/动作/断言结构。不是测试调用单个动作然后断言发生了什么，故事测试调用一系列动作，断言散布在这些调用之间。这违反了单元测试的经验法则，有些人可能认为这是神圣不可侵犯的——每个单元测试应该[只断言一件事](http://blog.jayfields.com/2007/06/testing-one-assertion-per-test.html)。然而，我发现在这种情况下，违反这个指导是可以的。如果使用得当，这种类型的测试在我的 redux 测试工具箱中提供了一个非常好的额外工具，在验证有状态行为时尤其方便——正如我们在测试 reducers 时经常发现自己想要做的那样。

## 精简我们的故事

虽然我喜欢这种测试方法，但我不喜欢我们目前在示例测试中不得不进行的手工状态处理。我们在测试过程中反复变异的那个单一的`state`变量感觉有点笨拙。让我们看看我们是否能做得更好。

如果我们后退一步，稍微眯着眼睛看一下，我们在测试中真正做的是重新实现 redux store，当我们使用它来应用我们的动作序列时，通过我们的 reducer 反复手动启动该状态变量。如果我们接受这种相似性，用真正的 redux 商店取代我们手摇商店的印象，会是什么样子？

```
import {createStore} from 'redux';

import {getTodoCounts} from './selectors';
import reducer, {addTodo} from './duck';

describe('todos duck', () => {
  it('supports multiple identical items', () => {
    const store = createStore(reducer);

    store.dispatch(addTodo('identical todo'));

    expect(getTodoCounts(store.getState()).total).toEqual(1);

    store.dispatch(addTodo('identical todo'));

    expect(getTodoCounts(store.getState()).total).toEqual(2);
  });
}); 
```

那就好多了！我们已经去除了一些干扰，将我们的测试浓缩到仅仅显示动作序列和相应的状态期望。

注意，对于使用类似于 [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) 的替代测试方法来说，这是不同的。我们使用一个标准的 redux store——不是某种类型的 test double 但是它充当了一种测试工具——一个隔离的环境，我们可以用它来以一种相当集成的方式验证我们正在测试的单元，同时仍然保持我们的测试的确定性和快速性。

这是我为 reducers 编写故事测试的首选方法。每个测试在一个新的 redux 存储中设置一个缩减器，然后将一系列动作分派到存储中，使用`getState`来验证缩减器正在进行的状态转换。

## 讲述一个更长的故事

正如我上面提到的，我发现故事测试是一种描述状态转换的表达方式，一系列的动作将使我们的商店经历这种状态转换。然而，到目前为止，我们的故事测试示例仍然相当贫乏，只涉及几个动作。这里有一个简单的例子，它给出了我们的 todo reducer 的一个更长的测试的想法:

```
import {createStore} from 'redux';

import {getTodoCounts,isTodoCompleted} from './selectors';
import reducer, {addTodo,completeTodo,deleteCompletedTodos} from './duck';

describe('todos duck', () => {
  it('marks a todo as completed, then deletes the completed todo', () => {
    const store = createStore(reducer);

    store.dispatch(addTodo({text:'todo a', id:'todo-a'}));
    store.dispatch(addTodo({text:'todo b', id:'todo-b'}));

    expect(getTodoCounts(store.getState())).toEqual({
      total: 2,
      complete: 0,
      incomplete: 2
    });

    store.dispatch(completeTodo({id: 'todo-a'}));

    expect(getTodoCounts(store.getState())).toEqual({
      total: 2,
      complete: 1,
      incomplete: 1
    });
    expect(isTodoCompleted(store.getState(),'todo-a')).toBe(true);

    store.dispatch(deleteCompletedTodos());

    expect(getTodoCounts(store.getState())).toEqual({
      total: 1,
      complete: 0,
      incomplete: 1
    });
  });
}); 
```

希望这能让你体验一下用故事测试方法编写的表达性缩减测试的类型。这些是快速的、解耦的单元测试，允许您验证我们的 actions 和 reducers 如何作为一个集成单元一起工作，在 redux 存储中运行，就像在真实的应用程序代码中一样。

我鼓励你自己尝试一下故事测试，并且[让我知道](https://twitter.com/ph1)它们是如何进行的！

* * *

1.  在本系列的前面，我展示了动作创建者和选择器组成了“superduck”的公共 API 一个封装了管理一组状态的缩减器、修改该状态的动作和查询该状态的选择器的模块。 [↩](#fnref1)*