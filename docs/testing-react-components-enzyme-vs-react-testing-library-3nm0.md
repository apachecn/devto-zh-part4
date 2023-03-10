# 测试 React 组件:酶与 React 测试库

> 原文：<https://dev.to/clarity89/testing-react-components-enzyme-vs-react-testing-library-3nm0>

*这篇文章最初发表在我的[个人博客](https://claritydev.net/blog/testing-react-components-enzyme-vs-react-testing-l)T3 上。*

在[的上一篇文章](https://dev.to/clarity89/build-your-own-unbeatable-tic-tac-toe-with-react-hooks-and-styled-components-4j1e)中，我们已经用 React 钩子和样式组件构建了一个井字游戏。然而，它缺少了开发过程中的一个关键部分——测试。在本帖中，我们将通过向 **TicTacToe** 组件添加测试来修复这一遗漏。此外，这似乎是一个比较两个最流行的 React 测试工具的好机会- [酶](https://github.com/airbnb/enzyme)和 [React 测试库](http://github.com/testing-library/react-testing-library)。作为一个复习，游戏的最终版本可以在这里找到并且代码可以在 [Github](https://github.com/Clarity-89/React_tic_tac_toe) 上获得。

这种比较的目的不是试图决定哪种框架是最好的，而是为了说明他们方法的不同。首先让我们安装软件包。

```
 npm i -D enzyme enzyme-adapter-react-16 @testing-library/react 
    @testing-library/jest-dom 
```

接下来，我们将在`src`目录的根目录下创建`__tests__`文件夹。我们将使用 [Jest](https://jestjs.io/) 来运行测试，它预装了 create-react-app，用于 Tic Tact Toe 游戏。在这里我们添加两个文件，每个测试框架一个: **TicTacToe.enzyme.test.js** 和 **TicTacToe.rtl.test.js.**

#### React 测试库

从 React 测试库开始，在 **TicTacToe.rtl.test.js** 中，我们将介绍一个基本的设置并编写第一个测试。但在此之前，我们需要回到 **TicTacToe.js** 做一个小小的修改，即给每个方块加上`data-testid`。

```
 // TicTacToe.js

    // ...

    {grid.map((value, index) => {
      const isActive = value !== null;

      return (
        <Square
          data-testid={`square_${index}`} // Add testid
          key={index}
          onClick={() => humanMove(index)}
        >
          {isActive && <Marker>{value === PLAYER_X ? "X" : "O"}</Marker>}
        </Square>
      );
    })}
    // ... 
```

这个`testid`是 React 测试库用于查询 DOM 元素的特殊属性。

```
 import React from "react";
    import { render, fireEvent, cleanup } from "@testing-library/react";
    import "@testing-library/jest-dom/extend-expect";
    import TicTacToe from "../TicTacToe";

    afterEach(cleanup);

    it("should render board with correct number of squares", () => {
      // Render the game component
      const { getAllByTestId, getByText } = render(<TicTacToe />);

      // Click 'X' to start game as player X
      fireEvent.click(getByText("X"));

      // Check that the correct number of squares is rendered
      expect(getAllByTestId(/square/).length).toEqual(9);
    }); 
```

如果你还记得以前的教程，当游戏开始时，玩家看到**选择你的玩家**屏幕。我们在这里通过选择 **X** 来做出选择，并验证网格是否以正确的方块数呈现。注意，我们也可以通过部分匹配获得条目，使用正则表达式语法- `getAllByTestId(/square/)` -返回所有在`testid`属性中包含`square`的条目。该库有大量关于可用查询类型的文档。

##### 测试异步动作

接下来，让我们验证一下，当我们点击一个空的方块时，那个玩家实际上走了一步。此外，我们可以测试计算机下一步行动。

```
 it("should register and display result of human player's move", async () => {
      const { getByTestId, getByText } = render(<TicTacToe />);
      fireEvent.click(getByText("X"));

      // Click the first square
      fireEvent.click(getByTestId("square_1"));

      // Validate that it has 'X' rendered
      expect(getByTestId("square_1")).toHaveTextContent("X");

      // Wait for computer move
      await waitForElement(() => getByText("O"));

      // Check that we have 'O' in the DOM
      expect(getByText("O")).toBeInTheDocument();
    }); 
```

在第一个方块上触发点击后，我们成功地验证了方块的文本内容是 **X** 。为了使用`toHaveTextContent`和其他一些有用的 Jest 匹配器，我们需要安装并导入 [Jest-dom](https://github.com/testing-library/jest-dom) 包。

玩家移动后，我们会测试电脑是否也移动了。在游戏组件中，电脑移动有轻微的延迟，这是由`setTimeout`造成的，所以我们需要使用测试库中特殊的异步工具。在这种情况下，我们将使用`waitForElement`函数等待计算机移动被渲染。此外，由于我们正在使用`await`，我们的测试函数必须被设为`async`。

请注意，尽管测试通过了，您可能仍然会在控制台中得到一个类似于`Warning: An update to TicTacToe inside a test was not wrapped in act(...)`的警告。这是因为在 React 16.9.0 之前,`act`测试工具只支持同步功能。因此，为了摆脱警告，只需将您的反应更新到最新版本。如果你对这个问题本身感到好奇，在 [Github](https://github.com/testing-library/react-testing-library/issues/281) 上有一个冗长的讨论。

接下来，我们将测试当玩家点击一个非空方块时，移动不会有任何影响。在这一点上，很明显我们需要写一些相同的代码来使人类玩家移动，然后等待计算机移动。当我们想测试最终游戏时会发生什么？我们要把所有的动作都编码到棋盘上吗？这听起来不像是一种有效的消磨时间的方式。相反，让我们修改 **TicTacToe** 组件来接受一个可选的网格，我们可以使用它来测试将游戏快进到任何状态。我们称它为`squares`(我已经没有其他名字了，因为*网格*和*板*已经被占用了)，它将默认为我们之前声明的`arr`。
T9】

```
 // TicTacToe.js

    // ...

    const arr = new Array(DIMS ** 2).fill(null);

    // ...

    const TicTacToe = ({ squares = arr }) => {
      const [grid, setGrid] = useState(squares);
      // ...
    } 
```

现在，当渲染组件进行测试时，我们可以提供一个带有预填充值的网格，所以我们不需要手动设置它们。有了这个设置，我们可以很容易地测试出移动到同一个方块并改变它的值是不可能的。

```
 // TicTacToe.rtl.test

    it("should not make a move if the square is not empty", () => {
      const { getByTestId, getByText } = render(
        <TicTacToe
          squares={[PLAYER_X, null, PLAYER_O, null, null, null, null, null, null]}
        />
      );
      fireEvent.click(getByText("X"));

      // Click non-empty square
      fireEvent.click(getByTestId("square_2"));

      // Should have initial value
      expect(getByTestId("square_2")).toHaveTextContent("O");
    }); 
```

为了使这个测试套件更加全面，我们还需要测试两件事情:

1.  当有赢的组合或平局时，显示结果的模式。
2.  按下**重新开始**按钮开始新游戏并显示初始屏幕。

对于第一个场景，我们将提供离游戏结束还有一步的网格状态，然后通过这一步我们测试游戏是否正确结束。

```
 // TicTacToe.rtl.test

    it("should correctly show Player X as a winner", async () => {
      // prettier-ignore
      const grid = [
        PLAYER_X, PLAYER_X, null,
        PLAYER_O, PLAYER_O, null,
        PLAYER_X, null,     PLAYER_O
      ];
      const { getByTestId, getByText } = render(<TicTacToe squares={grid} />);
      fireEvent.click(getByText("X"));

      // Make the winning move
      fireEvent.click(getByTestId("square_2"));

      // Wait for result modal to appear
      await waitForElement(() => getByText("Player X wins!"));

      // Check that result is declared properly
      expect(getByText("Player X wins!")).toBeInTheDocument();
    });

    it("should correctly display the draw result", async () => {
      // prettier-ignore
      const grid = [
        PLAYER_X, PLAYER_X, PLAYER_O,
        PLAYER_O, PLAYER_O, null,
        PLAYER_X, PLAYER_X, PLAYER_O
      ];
      const { getByTestId, getByText } = render(<TicTacToe squares={grid} />);
      fireEvent.click(getByText("X"));

      // Make the final move
      fireEvent.click(getByTestId("square_5"));

      // Wait for result modal to appear
      await waitForElement(() => getByText("It's a draw"));

      // Check that result is declared properly
      expect(getByText("It's a draw")).toBeInTheDocument();
    });

    it("should correctly show Player O as a winner", async () => {
      // prettier-ignore
      const grid = [
        PLAYER_O, null,     PLAYER_O,
        PLAYER_X, PLAYER_O, PLAYER_X,
        null,     PLAYER_X, null
      ];
      const { getByTestId, getByText } = render(<TicTacToe squares={grid} />);
      fireEvent.click(getByText("X"));

      // Make the move
      fireEvent.click(getByTestId("square_6"));

      // Wait for result modal to appear
      await waitForElement(() => getByText("Player O wins!"));

      // Check that result is declared properly
      expect(getByText("Player O wins!")).toBeInTheDocument();
    }); 
```

为了完整起见，我们正在测试所有 3 个可能的结局场景。请注意，网格的格式与游戏的网格相同，因此更容易看到游戏的状态。如果你使用[更漂亮的](https://prettier.io/)进行代码格式化，你可以用`// prettier-ignore`禁用它，以保持自定义格式。

请注意，在最后一个测试中，我们设置了一个棋盘，这样在人类玩家移动后，留给计算机移动的两个选项都将使它获胜。我们不必明确地等待轮到计算机，而是等待模态出现，这应该发生在最后一步棋之后。

作为最后的测试，我们确认游戏在按下**重新开始**按钮后被重置。

```
 // TicTacToe.rtl.test

    it("should start a new game after 'Start over' button is pressed", async () => {
      // prettier-ignore
      const grid = [
        PLAYER_O, null,     PLAYER_O,
        PLAYER_X, PLAYER_O, null,
        null,     PLAYER_X, PLAYER_X
      ];
      const { getByTestId, getByText } = render(<TicTacToe squares={grid} />);
      fireEvent.click(getByText("X"));

      // Make the winning move
      fireEvent.click(getByTestId("square_6"));

      await waitForElement(() => getByText("Start over"));
      fireEvent.click(getByText("Start over"));

      await waitForElement(() => getByText("Choose your player"));
      expect(getByText("Choose your player")).toBeInTheDocument();
    }); 
```

完成后，我们有了一个很好的综合测试套件，我们使用了 React 测试库，并以最终用户与之交互的相同方式测试了游戏。

#### 酶

现在我们将从最终用户的角度用 Enzyme 来测试这个游戏。我们首先将 **TicTacToe.enzyme.test.js** 文件添加到`__tests__`文件夹中。在编写实际的测试之前，我们需要做一些设置，即配置 React 的酶适配器。

```
 // TicTacToe.enzyme.test.js 

    import { configure } from "enzyme";
    import Adapter from "enzyme-adapter-react-16";

    configure({ adapter: new Adapter() }); 
```

确保使用与 React 当前版本相同版本的适配器。在初始设置之后，我们可以开始编写测试。让我们遵循与 React 测试库相同的路径，验证游戏在选择玩家后以正确大小的网格开始。

```
 // TicTacToe.enzyme.test

    import React from "react";
    import { mount } from "enzyme";
    import { configure } from "enzyme";
    import Adapter from "enzyme-adapter-react-16";
    import TicTacToe from "../TicTacToe";
    import { PLAYER_O, PLAYER_X } from "../constants";

    configure({ adapter: new Adapter() });

    it("should render board with correct number of squares", () => {
      // Render the game component
      const wrapper = mount(<TicTacToe />);

      // Find the 'X' button
      const buttonX = wrapper.findWhere(
        component => component.name() === "button" && component.text() === "X"
      );

      // Press it
      buttonX.simulate("click");

      // Check that board is rendered
      expect(wrapper.find("Square").length).toBe(9);
    }); 
```

从第一次测试可以明显看出，用酶测试组件，就像我们用 React 测试库一样，会更有挑战性。首先，我们需要使用强大的`findWhere`方法来查找带有特定文本的项目。还需要检查它实际上是一个按钮，这样我们就不会捕捉到任何包装组件。然后，为了得到`Square`组件，我们需要首先[覆盖它们的 displayName 方法](https://github.com/styled-components/styled-components/issues/896#issuecomment-332348516)。

```
 // TicTacToe.js

    const Square = styled.div`
     // ...
    `;

    Square.displayName = "Square"; 
```

我们也可以通过组件引用找到它们，但是在这种情况下，我们必须导出`Square`组件并直接将其导入到测试中。另一个选择是使用类似于`wrapper.find('div[data-testid^="square"]`的查询，来匹配以“square”开头的测试 id，其中`^=`用于匹配部分属性，然而这看起来一点也不漂亮。

我们在这里也使用了`mount`而不是`shallow`，它对组件及其子组件进行完整的 DOM 渲染，这在我们需要研究我们的样式组件时很有用。

按照与使用 React 测试库时相同的测试结构，我们现在将验证玩家的移动是否被正确渲染。

```
 // TicTacToe.enzyme.test

    it("should register and display result of human player's move", () => {
      // Render the game component
      const wrapper = mount(<TicTacToe />);
      const buttonX = wrapper.findWhere(
        component => component.name() === "button" && component.text() === "X"
      );
      buttonX.simulate("click");

      const firstSquare = wrapper.find("Square").at(0);

      // Click the first square
      firstSquare.simulate("click");

      // Validate that it has 'X' rendered
      expect(firstSquare.text()).toBe("X");
    }); 
```

既然可以通过显示名称选择样式化的组件，那么使用`at`选择器就可以很容易地获得特定索引处的组件。之后，我们可以使用`text()`方法断言它的文本内容是正确的。

还有一件事:看起来我们将在相当多的地方使用我们的详细按钮查找方法，所以让我们把它转换成一个实用函数。

```
 // TicTacToe.enzyme.test.js

    // Helper function to get button by a text
    const findButtonByText = (wrapper, text) => {
      return wrapper.findWhere(
        component => component.name() === "button" && component.text() === text
      );
    }; 
```

在这之后，我们可以用更少的代码通过特定的文本得到按钮。让我们继续检查玩家是否不能移动到被占领的方格。

```
 // TicTacToe.enzyme.test

    it("should not make a move if the square is not empty", () => {
      const wrapper = mount(
        <TicTacToe
          squares={[PLAYER_X, null, PLAYER_O, null, null, null, null, null, null]}
        />
      );
      const buttonX = findButtonByText(wrapper, "X");
      buttonX.simulate("click");

      // Get non-empty square
      const nonEmptySquare = wrapper.find("Square").at(2);

      // Click it
      nonEmptySquare.simulate("click");

      // Check that text content stays the same
      expect(nonEmptySquare.text()).toBe("O");
    }); 
```

##### 测试异步动作

测试通过了，所以我们都很好。接下来，我们将检查所有的残局组合是否被正确处理。

```
 // TicTacToe.enzyme.test

    import { act } from "react-dom/test-utils";

    // ...

    jest.useFakeTimers();

    it("should correctly show Player X as a winner", () => {
      // prettier-ignore
      const grid = [
        PLAYER_X, PLAYER_X, null,
        PLAYER_O, PLAYER_O, null,
        PLAYER_X, null,     PLAYER_O
      ];
      const wrapper = mount(<TicTacToe squares={grid} />);
      const buttonX = findButtonByText(wrapper, "X");
      buttonX.simulate("click");

      // Make the winning move
      wrapper
        .find("Square")
        .at(2)
        .simulate("click");

      // Wait for result modal to appear
      act(() => {
        jest.runAllTimers();
      });
      wrapper.update();

      // Check that result is declared properly
      expect(wrapper.find("ModalContent").text()).toBe("Player X wins!");
    });

    it("should correctly display the draw result", () => {
      // prettier-ignore
      const grid = [
        PLAYER_X, PLAYER_X, PLAYER_O,
        PLAYER_O, PLAYER_O, null,
        PLAYER_X, PLAYER_X, PLAYER_O
      ];
      const wrapper = mount(<TicTacToe squares={grid} />);
      const buttonX = findButtonByText(wrapper, "X");
      buttonX.simulate("click");

      // Make the final move
      wrapper
        .find("Square")
        .at(5)
        .simulate("click");

      // Wait for result modal to appear
      act(() => {
        jest.runAllTimers();
      });
      wrapper.update();

      // Check that result is declared properly
      expect(wrapper.find("ModalContent").text()).toBe("It's a draw");
    });

    it("should correctly show Player O as a winner", () => {
      // prettier-ignore
      const grid = [
        PLAYER_O, null,     PLAYER_O,
        PLAYER_X, PLAYER_O, PLAYER_X,
        null,     PLAYER_X, null
      ];
      const wrapper = mount(<TicTacToe squares={grid} />);
      const buttonX = findButtonByText(wrapper, "X");
      buttonX.simulate("click");

      // Make the move
      wrapper
        .find("Square")
        .at(6)
        .simulate("click");

      // Wait for the computer move
      act(() => {
        jest.runAllTimers();

        // Run timers again for the result modal to appear
        jest.runAllTimers();
      });

      wrapper.update();

      // Check that result is declared properly
      expect(wrapper.find("ModalContent").text()).toBe("Player O wins!");
    }); 
```

用酶测试异步组件的作用被证明是一个相当大的挑战。首先，我们需要将显示名称 prop 添加到模态内容组件:`ModalContent.displayName = "ModalContent";`因为我们不仅要测试状态是否已经正确更新，还要测试状态本身是否在超时后被设置，所以我们需要利用 Jest 的`useFakeTimers()`方法来模拟组件中使用的计时器。为了手动运行这些定时器，我们将使用来自 React TestUtils 的`act`函数中的`runAllTimers()`。此外，我们需要再次触发计时器来计算计算机的移动，并最终调用 Enzyme 的`update`方法来强制组件重新渲染，确保状态得到更新。

**提示:**如果您在确定测试不应该失败的时候还在想为什么测试会失败，Enzyme 的包装器有一个方便的`debug()`方法，可以打印出呈现在 DOM 中的组件。可以这么用`console.log(wrapper.debug()).`

最后的测试是断言游戏正确重启。

```
 // TicTacToe.enzyme.test.js

    it("should start a new game after 'Start over' button is pressed", () => {
      // prettier-ignore
      const grid = [
        PLAYER_O, null,     PLAYER_O,
        PLAYER_X, PLAYER_O, null,
        null,     PLAYER_X, PLAYER_X
      ];
      const wrapper = mount(<TicTacToe squares={grid} />);
      const buttonX = findButtonByText(wrapper, "X");
      buttonX.simulate("click");
      // Make the winning move
      wrapper
        .find("Square")
        .at(6)
        .simulate("click");

      act(() => {
        jest.runAllTimers();
      });

      // Re-render component
      wrapper.update();

      // Get restart button and click it
      const restartButton = findButtonByText(wrapper, "Start over");
      restartButton.simulate("click");

      // Verify that new game screen is shown
      const choosePlayer = wrapper.findWhere(
        component =>
          component.name() === "p" && component.text() === "Choose your player"
      );
      expect(choosePlayer.length).toBe(1);
    }); 
```

#### 结论

我们看到，用酶和 React 测试库测试 React 组件，而不需要了解太多实现细节是可能的。由于它的设计，用酶来做更有挑战性。对于酶，我们仍然通过它们的名称来获取成分，如果这些名称在未来发生变化或者成分被移除，我们的测试将会中断。此外，随着开发人员远离基于类的组件，许多 Enzyme 的测试类实例的方法不再有用，因为它们不适用于功能组件。

然而，仍然有可能用酶进行全面的测试。我个人已经开始用酶测试 React 组件，但是由于上面提到的原因，现在我更多地转向 React 测试库。最终，您的选择将取决于个人偏好和测试组件的结构。

希望本文通过举例说明两个最流行的框架的应用，使选择测试 React 组件的框架的任务变得更容易。

对这篇文章有任何问题/评论或其他类型的反馈吗？请在评论中或在 [Twitter](https://mobile.twitter.com/Clarity_89) 上告诉我。