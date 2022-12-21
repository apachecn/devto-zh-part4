# 反应钩井字游戏

> 原文：<https://dev.to/arrudaricardo/react-hooks-tic-tac-toe-m09>

## 简介

React Hooks 是 React 16.8 版的新增功能，允许在函数中使用状态。我个人更喜欢在 Javascript 中使用函数而不是类，现在我们可以做到了。
本教程的想法是使用 React 钩子重现游戏[井字游戏](https://en.wikipedia.org/wiki/Tic-tac-toe)。

## 设置

在本教程中，我们将使用用户[创建-反应-应用](https://github.com/facebook/create-react-app)，所以一定要在你的电脑上安装[节点和 NPM](https://nodejs.org/en/) 。

在您的终端中执行命令:

```
npx create-react-app tic-tac-toe
cd tic-tac-toe 
```

现在我们创建了一个 React 应用程序，用你选择的文本编辑器打开目录，例如 [vim](https://www.vim.org/)

## 创建组件

你所有的源文件都在目录下。让我们为组件创建一个新目录。

在井字游戏/src 中创建一个新的/components 目录和两个新文件 **Table.js** 和 **TicBlock.js** 。
table . js 将是一个包装 TicBlock.js 的组件，Ticblock.js 将是用户可以添加 X 或 o 的 9 个块中的每一个

我们需要首先将表格导入到 App.js 中。

App.js

```
import React from 'react';
// Allow us to use JXS
import './App.css';
// Import the Style
import Table from './components/Table'
// Import the Table.js components
function App() {
  return (
      <div>
          <Table />
       </div>
  );
}

export default App;
//export in order to be imported by index.js, that is the root file for this App. 
```

如果您需要更多关于 JSX 的信息，请点击此[链接](https://reactjs.org/docs/introducing-jsx.html)。

知道我们可以编辑 **Table.js** 组件。
首先我们需要导入钩子[用户状态](https://reactjs.org/docs/hooks-reference.html#usestate)和[用户效果](https://reactjs.org/docs/hooks-reference.html#useeffecthttps://reactjs.org/docs/hooks-reference.html#useeffect)。钩子*是让你从函数组件* <sup id="fnref1">[1](#fn1)</sup>
中“钩入”反应状态和生命周期特性的函数。对于表，我们可以设置三个使用状态，表和表的子表将具有访问权限。

```
 const [table, setTable] = useState([0,0,0, 0,0,0, 0,0,0])
    const [winner, setWinner] = useState([])
    const [last, setLast] = useState(Math.random() > 0.5? 5:3) 
```

*表*和*可设置*将管理 X 和 0 的表状态。
*赢家*和 *setWinner* 将跟踪赢家
*last* 和 *setLast* 将跟踪最后一步棋 beeing 整数 3 映射 X
和 5 映射 o .并将随机 last 设置为 5 或 3。

现在我们可以使用 React 钩子 *useEffect* ，它将在每次*表*状态改变时运行。
*use effect*将检查游戏是否结束，如果一行的总和达到 9，3x3，我们 X 是赢家如果
总和达到 15，3x5，我们知道 O 是赢家，这同样适用于对角线。

```
 useEffect(()=>{
        //checking winner row and col
        for (let i = 0; i <= 2; i++){
            const idx = (i % 3) * 3 // -> 0,3,6
            //check row
            if ( (table[idx] + table[idx+1] + table[idx+2] )=== 9 || (table[idx] + table[idx+1] + table[idx+2] ) === 15){
                setWinner([idx,idx+1,idx+2])
                gameOver()
            }
            //check col
            if ((table[i] + table[i+3] + table[i+6] )=== 9 || (table[i] + table[i+3] + table[i+6] ) === 15){
                setWinner([i,i+3,i+6])
                gameOver()
            }
        }
        //checking winner diagonal
        if ((table[0] + table[4] + table[8] ) === 15 || (table[0] + table[4] + table[8] ) === 9 ){
            setWinner([0, 4, 8])
            gameOver()
        }
        if ((table[2] + table[4] + table[6] ) === 9 || (table[2] + table[4] + table[6] ) ===15){
            setWinner([2, 4, 6])
            gameOver()
        }
        // check if table completed
        if (table.indexOf(0) === -1){
            gameOver()
        }
    }, [table]) 
```

最后一个参数*【table】*是 React 的组件，它将检查并运行*【use effect】*
，以获取本例中*【table】*中的任何更新。

对于这些块，我们创建了一个组件调用 *TicBlock* ，它有一个唯一的属性*编号*
，将接收*最后*和*设置最后*钩子的*最后*，以及将接收钩子*表*和*设置*的*表*。

```
<TicBlock number={0 to 9} last={[last,setLast]} table={[table,setTable]} winner={winner}/> 
```

对于 *TicBLock* 我们将定义获取父*表* :
的道具

```
 const number = props.number 
    const [last, setLast] = props.last
    const [table, setTable] = props.table
    const winner = props.winner 
```

所以我们可以用道具来检查我们是否渲染了一个空的空间，X 或者 O

如果是圆:

```
<Circle fill={winner.indexOf(number) !== -1? 'red':""} width="100%" height="100%"/> 
```

如果是 X:

```
<x fill={winner.indexof(number) !== -1? 'red':""} width="100%" height="100%"/> 
```

现在我们需要一个 onClick 事件监听 *TicBLock* 并将
状态*可设置*为 X 或 O:

```
<div className="ticblock" onClick={() => {
    if (table[number] === 0 && winner.length === 0) {
    setTable(( () => {
        let newTable = [...table];
        let newLast = last === 3? 5:3
        newTable[number] = newLast
        setLast(newLast)
        return newTable
    })())} 
```

你可以在这里测试游戏[。](https://codesandbox.io/s/github/arrudaricardo/tic-tac-toe-react-hook/tree/master/?fontsize=14)

* * *

1.  [https://reactjs.org/docs/introducing-jsx.html](https://reactjs.org/docs/introducing-jsx.html)↩