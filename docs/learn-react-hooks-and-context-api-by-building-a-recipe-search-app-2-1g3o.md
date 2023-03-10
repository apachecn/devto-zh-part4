# 通过构建食谱搜索应用程序学习 React 挂钩和上下文 API)

> 原文：<https://dev.to/mongopark/learn-react-hooks-and-context-api-by-building-a-recipe-search-app-2-1g3o>

这是学习 **React 钩子**和**上下文 API** 系列的第二部分。在[的第一部分](https://dev.to/mongopark/let-s-learn-react-hooks-and-context-api-by-building-a-recipe-search-app-39pc)中，我们学习了`useState`的基础知识，构建了食谱搜索应用程序的第一部分。完整代码可以在 [github](https://github.com/olajohn-ajiboye/Blog-React-Hook-Tutorial) 上找到

## 概述我们将在本节中学习的内容

在本节中，我们将对`states`做更多的工作，以便完全理解这个概念。我们来解释一下`useEffect`。构建`RecipeSearch`组件。然后，我们将重构我们的状态管理策略，以利用`Context API(useContext)`。激动人心吧？

[![ezgif.com-resize.gif](img/cc6c1a89e08c34b6d2a8593e253b5369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xHAVm6lC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561923790914/kbyoLT-ka.gif)

## 第一件事...

从现在开始，我们不会与经典组件做太多的比较。相反，我们将更专注于学习`hooks`的基础知识。从上一部分，我已经将`RecipeDetails`提取到它自己的组件中。这有两个原因

*   这是应该做的事情，这是基于组件的框架的要点。它允许代码重用。
*   同时，这也给了我们足够的机会来发现传递道具所产生的问题。以及上下文 API 如何帮助我们。稍后将详细介绍这一点！首先，让我们尝试一下`useEffect`

# `useEffect`钩子的快速入门

在第一部分中，我们提到并使用了`useEffect`钩子，但没有给出任何详细的解释。我确信只有少数人意识到我们在第一部分中使用 useEffect 钩子的方式有问题。

> 提示:还记得我们是如何创建我们自己单独的 JSON 响应的吗？以免 API 调用太多？。

哈哈，我们只是像以前一样使用`useEffect`就犯了大错。我稍后会深入探讨这个问题。

但是首先，这个`useEffect`钩子是什么？。顾名思义，它一定和**特效**有关吧？，如果你是这样猜测的，那你绝对是正确的。但是什么样的影响呢？。

根据 React [官方文档](https://reactjs.org/docs/hooks-effect.html)，效果是与数据获取、设置订阅和手动更改 React 组件中的 DOM 相关的动作。这些都是副作用的例子。有人称之为“副作用”，也有人称之为“影响”。我们指的是同一件事。如果你熟悉 React 类生命周期方法，你可以把`useEffect`钩子看作是`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。

## 正确使用`useEffect`挂钩。

让我们将这些信息与我们的应用程序联系起来。在 App.js 中，我们从 food2fork API 函数中获取数据，然后在`useEffect`函数中调用该函数。这相当于在`componentDidMount`函数内部调用它。让我们仔细看看。

但是想一想。`componentDidMount`的预期用途是什么？，答案就在名字里！。当组件被挂载时，您只需要运行这个函数中的任何函数。让我们慢慢地检查那个信息。**挂载**——is 阶段组件被创建(你的代码，和 react 的内部)，然后插入到 DOM 中。

但是，如果你有一个函数在一个`componentDidMount`中引起重复的效果，你的组件将不断地重新渲染，除非你找到一种方法来清理它。这通常在`componentWillUnmount`生命周期内完成。

那么`useEffect`是如何在一个函数中取代`componentDidMount`和`componentWillUnmount`的呢？让我们回顾一下我们对 useEffect 的错误使用，以便更好地理解这一点。当我们像下面这样调用`useEffect`时，组件不断重新渲染，因为它不知道如何*卸载*。这是一个可怕的性能瓶颈。

```
 // this isn't quite right. It works, but we'll see the problem later on
useEffect(() => {
    fetchRecipe()
  }) 
```

让我们做一个事后分析来看看这个问题在起什么作用。在 *App.js* 中的`fetchRecipe()`内，尝试控制台记录一些指示组件何时呈现的内容。我们也可以只检查浏览器中的网络选项卡。但是有时控制台日志记录并观察它的运行可能会更快地理解这一点。

回购的`third/hook-in-app.js`分支上。编辑`App.js`添加 console.log，弹出打开你的控制台。对于这个小实验，你应该在 **App.js** 中有类似下面的东西。

```
import React, { useState, useEffect } from 'react';
import RecipeList from './components/RecipeList'
import RecipeDetails from './components/RecipeDetails'

function App() {
  const apiKey = `36920f6651c9cd9d91a6c3205cabaa19`
  let url = `https://www.food2fork.com/api/search?key=${apiKey}`
  const [showHomeButton, setShowHomeButton] = useState(false)
  const [recipes, setRecipes] = useState([])
  const [loading, setLoading] = useState(true)
  const [search, setSearch] = useState('')

  const fetchRecipe = async () => {
      const recipeData = await fetch(url)
      const { recipes } = await recipeData.json()
      setRecipes(recipes)
      setLoading(false)
      console.log("I am beign rendered again and again")
  }

  useEffect(() => {
    fetchRecipe()

  })

  return (
    <div>
      {loading ? <h1 className="text-center">...fetching {search} Recipe</h1> :
        <RecipeList recipes={recipes} />}
    </div>
  );
} 
```

你会得到下面的恐怖。到底发生了什么事？这甚至是不可接受的行为。由于我创建的 **JSON** 响应，我们侥幸逃脱了。我们可以无限制地打`API`电话，没有任何问题。显然，这不可能是生产中正确的做事方式。那么问题出在哪里？甚至是解决方案。

[![re-render.gif](img/d92256fdf3503a2d23ea811b2ee6aba0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OoCtrUgH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561974360255/9GWqu9bta.gif)

显而易见的是，`fetchRecipe`不断尝试从 API 获取响应，并且每次都由`useEffect`重新呈现。即使一切都没有改变。我们自然需要在一个类组件中通过在另一个`componentWillUnmount`中取消 API 调用来清理这个问题。我们来看看`useEffect`是如何处理这个问题的。

既然我们已经认识到了这个问题，让我们来考虑一下解决办法。暂停一会儿，我们实际上打算实现什么？在我们的例子中，我们希望`fetchRecipe`函数在组件挂载时只运行一次——即获取数据并显示它。或者当任何事情对函数的结果有影响时。在这一点上，没有什么影响结果。那么，我们如何克服这个问题呢？事实证明非常简单。

在`useEffect`函数中，我们简单地传递一个空数组作为第二个参数。因此，我们指示`useEffect`不要更新，除非当组件第一次被安装时，因为我们不希望任何东西导致获取配方函数更新。就像下面这样。

```
 useEffect(() => {
    fetchRecipe()
  }, []) // pass an empty array here. 
```

现在，如果您返回到`App.js`并运行 console.log，您会发现`fetchRecipe`只运行一次。

[![ezgif.com-video-to-gif (1).gif](img/c9a2569eb5a0fd30cfe5480bfc0e95e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HsWqqyr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561977935130/TrKY4KO2_.gif)

这很好，对我们的案子很有帮助。但是如果我们想在某些事情导致`fetchRecipe`改变后更新和重新渲染呢？你可能会问这是什么？假设我们有一个状态，它改变了获取数据的 *url* 。这意味着来自`fetchRecipe`的数据依赖于`url`。我们只是简单地将数组中的参数传递给`useEffect`。因此，我们可以根据需要向数组中传递任意多个参数。

```
 useEffect(() => {
    fetchRecipe()
  }, [url,query]) 
```

> 你用人类的语言对`useEffect`说的是:只有当`url`或`query`参数改变时才更新。

[![solved.gif](img/713ca9029e06d39a669c301d965cb697.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D5quCMyf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561978591770/BTRAroWZe.gif)

如果你仍然感到困惑，我推荐 [RobinWieruch](https://www.robinwieruch.de/react-hooks-fetch-data/) 教程以获得更多的例子。

回到我们的应用程序...

# 让我们来构建`RecipeSearch`组件。

有了安全获取数据的知识，我们现在将切换到使用 food2fork 的 API 键。因此，我们将在`App.js`中做一些重构。我们还将介绍在异步函数中有一个`try catch`块来捕捉任何错误的最佳实践。

> 克隆回购的`4/feature/implemented-search`分支以跟进。

首先，让我们考虑一下我们希望搜索组件实现什么，然后考虑如何实现这一点。所以，我们想输入一个搜索，当我们点击提交或按回车键时，我们想把页面的内容改为新的搜索结果。我们还希望能够返回到前 30 个食谱，从而清除搜索(或者返回到前 30 个结果，这取决于你怎么想)。下面明白我的意思了。

[![ezgif.com-video-to-gif (1).gif](img/c93a1a44251a082810110ce354c2b1f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ESYhs-bN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561987859280/PRfjvgOML.gif)

现在，我们心中有一个目标，我们如何用`hooks`写代码。请注意，我试图使这个尽可能的基础，这样，即使有非常基本的反应知识，你仍然可以建立这个。所以并不是所有的事情都是特定的。

很快，很明显，我们肯定需要一些州来管理所有这些变化。首先，我们获取与我们的搜索查询相关的另一组 30 个**食谱**。这是一个州。我们还必须决定`Go back Home`按钮何时出现或消失，你猜对了，伙计！。那是另一种状态。最后，也许是最重要的`state`是保存我们的`search`参数。

像以前一样，让我们看看代码在`App.js`中的样子，然后逐行解释。搜索功能的完整代码[此处](https://github.com/olajohn-ajiboye/Blog-React-Hook-Tutorial/blob/feature/implemented-search/src/App.js)

```
import React, { useState, useEffect } from 'react';
import RecipeList from './components/RecipeList';
import './App.css'

function App() {
  // remeber to replace key
  const apiKey = `36920f6651c9cd9d91a6c3205cabaa19`
  let url = `https://www.food2fork.com/api/search?key=${apiKey}`
  const [showHomeButton, setShowHomeButton] = useState(false)
  const [recipes, setRecipes] = useState([])
  const [loading, setLoading] = useState(true)
  const [search, setSearch] = useState('')
  const [error, setError] = useState('')

  const fetchRecipe = async () => {
    try {
      const recipeData = await fetch(url)
      const { recipes } = await recipeData.json()
      setRecipes(recipes)
      setLoading(false)

    } catch (e) {
      if (e) {
        setError(e.message)
        console.log(error)
      }
    }
  }

  const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      setLoading(true)
      const searchUrl = `${url}&q=${search}`
      const searchedRecipeData = await fetch(searchUrl)
      const { recipes } = await searchedRecipeData.json()
      setRecipes(recipes)
      setLoading(false)
      setShowHomeButton(true)
    } catch (e) {
      console.log(e)
    }
  }

  const handleSearchChange = (e) => {
    setSearch(e.target.value)
  }

  const handleReturnHome = () => {
    fetchRecipe()
    setShowHomeButton(false)
  }

  useEffect(() => {
    fetchRecipe()

  }, [])

  return (
    <div>
      {loading ? <h1 className="text-center">...fetching {search} Recipe</h1> :
        <RecipeList
          search={search}
          handleSubmit={handleSubmit}
          handleSearchChange={handleSearchChange}
          recipes={recipes}
          showHomeButton={showHomeButton}
          handleReturnHome={handleReturnHome} />}
    </div>
  );
}

export default App; 
```

注意到，我们如何改变了一些事情。首先，我们现在安全地直接发出 API 请求。因此，我们使用真正的 API 键。无论如何，我们不能继续使用我的`json`的原因将变得很明显。让我们一条一条来。

我们已经为`url`设置了一个变量。我们从 [food2fork](https://www.food2fork.com/about/api) 文档中知道了 URL 端点的样子。接下来，我们为`showHomeButton`设置一个状态来处理 home 按钮的可见性。并将初始值设置为`false`。也就是说，
最初当我们进入主页时，没有必要显示主页按钮。接下来的两只`states`已经很熟悉了。然后我们有了`search`状态，我们将原始值设置为一个空字符串。因为我们第一次访问主页时还没有搜索任何内容。

接下来，我们必须管理如何响应用户在搜索查询中输入的内容。我们已经设置了`handleSearchChange`函数来处理这个问题。我们使用`handleSubmit`按钮来更新列表中的数据，以匹配搜索结果。最后，我们有`handleReturnHome`函数，显然，你猜对了。用 30 种顶级食谱帮助我们安全回家。

让我们检查一下每个功能是如何履行其职责的。

第一，`handleSearchChange`。目的是捕获用户输入，然后将`search`状态更新为等于用户输入。这非常简单，因为函数体只有一行。

```
 const handleSearchChange = (event) => {
    setSearch(event.target.value)
  } 
```

它是这样工作的，因为我们响应用户输入，我们可以从事件对象访问`onChange`属性。我们简单地从`event.target.value`中获取值，并使用`setSearch hook`用这个值更新搜索状态。然后我们需要将它们作为道具一路传递给`RecipeSearch`组件，如下所示。这是需要和`Context API`一起解决的事情之一。

接下来，让我们检查 App.js 中最有趣的函数`handleSubmit`。它是做什么的？先看代码再解释。

```
 const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      setLoading(true)
      const searchUrl = `${url}&q=${search}`
      const searchedRecipeData = await fetch(searchUrl)
      const { recipes } = await searchedRecipeData.json()
      setRecipes(recipes)
      setLoading(false)
      setShowHomeButton(true)
    } catch (e) {
      console.log(e)
    }
  } 
```

因为这将在提交时触发。我们可以访问`event`对象。首先，我们需要防止`onSubmit`的默认行为，即重新加载页面，因此我们有了`e.preventDefault()`。我们将加载状态设置为 true，用`setLoading(true)`表示我们仍在获取数据。然后我们取`search`的当前状态，它现在等于用户的输入。我们用它来创建一个新的 API 点，它基于我们从 food2fork 获得的文档。然后基于该搜索查询获取新数据。然后，它用`setRecipe`更新当前的`recipe`状态，以匹配来自搜索查询的新数据。现在我们有了数据，我们使用`setLoading(false)`将`loading`状态设置为假。现在新的食谱列表是基于搜索查询的，我们需要一个回家的方法。因此，我们使用`setShowHomeButton`将`showHomeButton`设置为真。

最后，我们有`handleReturnHome`函数，它的工作是让我们带着前 30 个食谱列表回家。因此，我们简单地像最初那样获取 recipe，这已经将`recipe`状态设置回原来的状态。然后我们将`showHomeButton`设置回 false，当我们在主页上时，这个按钮就消失了。然后，我们简单地将所有需要的状态作为道具传递给我们要使用它们的子组件。

## **`The Context API`T4】**

React 上下文 API 本质上是一种创建全局状态的方法，可以在应用程序中的任何地方使用。这是把道具从爷爷奶奶传给孩子等等的一种替代方式。它被吹捧为 Redux 的更轻便的替代品。根据我的经验，我会说它更像 Vue 的 VueX。在那里你有一个单一的真相来源，可以访问全球的状态。

让我们检查一下我们的应用程序中道具钻孔的情况。你可以想象在一个如此简单的应用程序中，我们已经必须将道具从`App.js`传递到`RecipeList`，然后传递到`RecipeSearch`。即使 RecipeList 组件不需要`handlesSubmit`和`handlesSearchChange`道具，我们仍然需要传递它们，因为它是`RecipeSearch`的父组件。想象一下嵌套更深的组件树，那么你已经可以看到混乱。像 Redux 这样的库有助于解决这个问题，但是 context 是一个简单的轻量级版本。

[![Drilling.png](img/654273feb89debb4cdb3cb9a383c1873.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZenEC3Cp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1562193246399/6iH63N_v0.png)

## *使用上下文*勾去救援

那么，如果我们可以在组件树中更高的位置访问我们想要的任何状态，并且只在需要的地方使用它们，会怎么样呢？这是我们接下来要对`useContext hook`做的事情。关于上下文 API，有几件事情需要理解。

*   Context 旨在共享数据，这些数据可以被视为 React 组件树的“全局”数据。
*   当不同嵌套层次的许多组件需要访问某些数据时，主要使用上下文

我们来讨论一下上下文给我们的 API。首先我们有`React.createContext`。它创建一个上下文对象。当 React 呈现订阅该上下文对象的组件时，它将从树中它上面最近的匹配提供者读取当前上下文值。

每个上下文对象都有一个提供者反应组件，允许消费组件订阅上下文变化。

`Context.Consumer` -订阅上下文变化的 React 组件。这允许您订阅功能组件中的上下文。

## 让我们将所有这些捆绑到我们的例子中，看看它是如何工作的。

首先，让我们创建一个上下文文件夹或一个包含 index.js 文件的文件。在`index.js`内部，我们需要创建一个`Provider`,它接受数据并向其中的所有子组件提供数据。首先，让我们将所有的数据获取逻辑从`App.js`移到上下文文件中。你应该会留下一个几乎空空如也的`App.js`，如下图。

```
import React, { useContext } from 'react';
import RecipeList from './components/RecipeList'
function App() {

  return (
    <div>
      {loading ? <h1 className="text-center">...fetching {search} recipe</h1> :
        <RecipeList />}
    </div>
  );
}
export default App; 
```

和`index.js`...

```
import React, { useState, useEffect } from 'react'

const RecipeContext = React.createContext()

const apiKey = `7cdab426afc366070dab735500555521`

const RecipeProvider = (props) => {
  let url = `https://www.food2fork.com/api/search?key=${apiKey}`
  const [showHomeButton, setShowHomeButton] = useState(false)
  const [recipes, setRecipes] = useState([])
  const [loading, setLoading] = useState(true)
  const [search, setSearch] = useState('')

  const fetchRecipe = async () => {
    try {
      const recipeData = await fetch(url)
      const { recipes } = await recipeData.json()
      setRecipes(recipes)
      setLoading(false)
      console.log(recipes)
    } catch (e) {
      if (e) {
        console.log(e.message, 'Try updating the API key in App.js')
      }
    }
  }
  const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      setLoading(true)
      const searchUrl = `${url}&q=${search}`
      const searchedRecipeData = await fetch(searchUrl)
      const { recipes } = await searchedRecipeData.json()
      setRecipes(recipes)
      setLoading(false)
      setShowHomeButton(true)
    } catch (e) {
      console.log(e)
    }
  }
  const handleSearchChange = (e) => {
    setSearch(e.target.value)
  }
  const handleReturnHome = () => {
    fetchRecipe()
    setShowHomeButton(false)
  }

  useEffect(() => {
    fetchRecipe()

  }, [])

  return (
    <RecipeContext.Provider value={{
      loading,
      search,
      showHomeButton,
      recipes,
      handleSearchChange,
      handleSubmit,
      handleReturnHome,
    }}>
      {props.children}
    </RecipeContext.Provider>
  )
}

export { RecipeProvider,  RecipeContext } 
```

### 我们仔细拆开上面的代码，试着理解一下。

我们只是简单地将所有的数据获取逻辑从我们的`App.js`移到了`index.js`中，这样就可以在全球范围内使用了。我们已经知道了流程，我们有了数据和状态，就像我们之前做的那样。

现在，到有趣的部分，在第二行。我们从`React.createContext()`方法中创建了`RecipeContext`。RecipeContext 只是一个变量，名称并不重要，给它一个与其功能相近的名称是一个很好的做法。当我们创建`RecipeContext`的时候，它给了我们另外两个属性，即`Provider`和`Consumer`。**提供者**为我们提供了一种方法，将所有数据和状态作为道具从 React 树的最顶端传递到我们想要使用的任何地方。因此，我们创建了`RecipeContext.Provider`，并将所有状态和数据作为一个名为* *value ** 的道具传递下去。然后，我们导出这些值以供使用。如下图。当我们开始使用它们时，情况会变得更清楚。

```
 <RecipeContext.Provider value={{
      loading,
      search,
      showHomeButton,
      recipes,
      handleSearchChange,
      handleSubmit,
      handleReturnHome,
    }}>
      {props.children}
    </RecipeContext.Provider>
  )
}

export { RecipeProvider,  RecipeContext } 
```

接下来，我们必须在组件层次结构中找到最高的树来传递**值**道具。只有通过这种方式，它才能把它传递给所有的孩子。那就是我们根中的`index.js`。这是`App`组件所在的位置。因此，App 组件能够将任何数据或状态作为道具传递到任何需要的地方。在你的应用程序的根中的`index,js`。你应该像下面这样把`RecipeProvider`包在 App 周围。

```
import React from 'react';
import ReactDOM from 'react-dom';
import { RecipeProvider } from './context/index'

ReactDOM.render(
  <RecipeProvider>
    <App />
  </RecipeProvider>
  , document.getElementById('root')); 
```

从这里开始，我们的上下文文件中传递的所有道具对`App`组件的所有子组件都是可用的，本质上是每个组件。令人兴奋的是我们将如何使用它。我们将用`RecipeList`和`RecipeSearch`组件做一个例子。您的 **RecipeList** 组件应该如下所示。

```
import React, { useContext } from 'react'
import Recipe from './Recipe'
import RecipeSearch from './RecipeSearch'
import { RecipeContext } from '../context/index'

export default function RecipeList() {
  const appContext = useContext(RecipeContext)
  const { showHomeButton, recipes, handleReturnHome } = appContext

  return (
    <>
      <RecipeSearch></RecipeSearch>
      <div className="container my-5 home-button">
        {showHomeButton && <button type="button"
          className="btn btn-warning"
          onClick={() => handleReturnHome()}>
          Go Back Home
        </button>}
        <div className=" d-flex d-flex justify-content-center mb-3">
          <h1 className="text-slaned ">Recipe List</h1>
        </div> 
        <div className="row recipe-list">
          {recipes.map(recipe => {
            return <Recipe
              key={recipe.recipe_id} recipe={recipe} />
          })}
        </div>
      </div>
    </>
  )
} 
```

我们现在已经从文件中导入了`RecipeContext`,并从 React 中导入了`useContext`。在函数内部，我们创建了一个变量来保存 RecipeContext 的值。然后我们只在 **RecipeList** 中获取我们需要的值。这在很多方面都很干净，其中两个是。

*   首先，我们直接去`RecipeList`我们需要那些状态和数据的地方，我们不需要把道具从父母传给孩子。
*   第二，我们只在组件中提取我们需要的道具，我们不需要给`RecipeList`一些它不需要的道具，而只需要传递给它的子组件。如果他们需要数据，我们就直接去找那个子组件。

让我们检查一下`RecipeSearch`组件。再一次，让我们看看代码，然后解释它。

```
import React, { useContext } from 'react'
import { RecipeContext } from '../context/index'

export default function RecipeSearch() {
  const appContext = useContext(RecipeContext)
  const { handleSubmit, handleSearchChange } = appContext

  return (
    <div className="container d-flex justify-content-center my-5">
      <form className="form-inline" onSubmit={(e) => handleSubmit(e)}>
        <label htmlFor="search" >Search:</label>
        <input data-age="Love" onChange={(e) => handleSearchChange(e)} type="text" className="form-control" id="search" placeholder="coffeee" name="search" />
        <button type="submit" className="btn btn-primary"><i className="fa fa-search"></i></button>
      </form>
    </div>
  )
} 
```

像在 **RecipeList** 中一样，我们已经导入了`useContext`和 **RecipeContext** 。我们创造了变量。看看这有多简洁明了，我们不需要从 **RecipeList** 中获取`handleSubmit`和`handleSearchChange`，我们只是简单地从上下文中抓取它们。

仅此而已。我们已经介绍了 3 种主要类型的钩子。*使用状态*、*使用效果*、*使用上下文*。我相信这些是理解使用 react 钩子的更高级更好的方法所必需的基础。我相信对于初学者来说，它们更干净，更容易消化。

如果你想更深入地了解。您可以重构`Recipe`组件来使用`useContext`。拿一个带有类组件的老项目，用钩子把它转换成功能组件。如果你想要更有挑战性的东西，学习一下`useReducer`钩子，以及我们如何在**上下文**文件中重构多个状态，以利用 **useReducer**

谢谢大家！。完整代码可以在 [github](https://github.com/olajohn-ajiboye/Blog-React-Hook-Tutorial) 上找到。欢迎评论和反馈。