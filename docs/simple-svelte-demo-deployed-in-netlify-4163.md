# 部署在 Netlify 中的简单苗条演示

> 原文：<https://dev.to/edades/simple-svelte-demo-deployed-in-netlify-4163>

我正在学习 Svelte，我创建了一个简单的项目来解释一些功能。

在开始之前，你总能在 git lab[https://gitlab.com/edades/cart-svelte](https://gitlab.com/edades/cart-svelte)找到源代码，在 Netlify
T3】https://silly-liskov-fc7aa8.netlify.com/找到部署的项目

### 打造苗条项目。

您有两个选择:
1-用 rollup 构建您自己的环境，并创建您的配置文件和脚本。

2-从苗条的回购获得一个干净/简单的项目。

```
npx degit sveltejs/template my-svelte-project
cd my-svelte-project
npm install
npm run dev 
```

### 文件夹结构

对于这个例子，我创建了以下结构:

-src
-组件
-content box . svelete
-header . svelete
-product . svelete
-Store
-Store . js
-Views
-home . svelete
-app . svelete
-main . js
-products . js

我来自 react world，我试图用容器(视图)和组件维护一个类似的文件夹结构。我还创建了一个 products.js 来获取静态产品数据。

主文件将应用程序放入 document.body

```
import App from './App.svelte';

const app = new App({
    target: document.body
});

export default app; 
```

在我们的 App.svelte 中，我们可以导入我们的(在本例中)容器组件(视图)。

```
<script>
    import HomeLayout from './Views/Home.svelte';
</script> 
<HomeLayout /> 
```

首先，我们有组件下面的 javascript 代码。

现在，让我们来看看 Views/Home.svelte 组件。

```
<script>
  import Header from '../Components/Header.svelte';
  import ContentBox from '../Components/ContentBox.svelte';
  import productsJson from '../products.js'
  import { onMount } from 'svelte';

  let products

  onMount(() => {
    products = productsJson
  })
</script> 
<div class="container-home">
  <Header />
  <ContentBox products={products} /> </div> 
```

首先，我们可以导入将要使用的组件，也可以导入产品 Json。我们还将使用一个来自生命周期的函数

`let products`是我们存储来自 productsJson 的数据(或来自任何服务的返回数据)的状态。
所以，在 onMount 函数中我们可以做这个赋值。

注意，在`<ContentBox products={products} />`中，我们将产品列表传递给组件。我们待会再看这个。

现在，让我们看看标题组件。在这里我们可以找到更多的乐趣。

这个组件比较大，如果想看完整的源代码，请到[https://git lab . com/edades/cart-svelte/blob/master/src/Components/header . svelte](https://gitlab.com/edades/cart-svelte/blob/master/src/Components/Header.svelte)

在代码的顶部，我们有样式规则。

```
<style>
  .header {
    height: auto;
    width: 100%;
    background: skyblue;
    box-sizing: border-box;
    padding: 7px;
  }
  .header .menu {
    display: flex;
    align-items: center;
    list-style-type: none;
    justify-content: flex-end;
  }
  .
  .
  .
</style> 
```

中间是 javscript 内容。

```
<script>
  const logoUrl = 'http://placehold.it/180x60';
  let menuOptions = [
    {
      title: 'Option 1',
      url: '#',
      active: false
    },
    {
      title: 'Option 2',
      url: '#',
      active: false
    },
    {
      title: 'Option 3',
      url: '#',
      active: false
    }
  ];

  const setActivate = ({ title }) => {
    menuOptions = menuOptions.map(item => ({
      ...item,
      active: item.title === title
    }))
  }
</script> 
```

`const logoUrl`是一个简单的常量，用于存储徽标 url，我们可以在代码中直接使用它。

`let menuOptions`是一种状态(在 vue 或 react 中)，在这里我们管理菜单选项。

`const setActivate`是一个修改状态的功能‘menu option’。我们传递一个标题，当标题等于菜单中的选择项时，从数组中设置该项。

在这之后，我们有了 html 代码。

```
<li class="logo"><img src={logoUrl} alt="logo" /></li>
{#each menuOptions as option}
  <li
    class="menuItem{option.active ? ' active' : ''}"
    on:click={() => setActivate(option)}>
     {option.title}
  </li>
{/each} 
```

1-我们使用 logoUrl 常量来获取徽标。
2-我们使用`#each`来迭代 menuOptions 数组。这是一个来自 svelte 的函数。
3-我们使用三元运算符对菜单中的活动项目使用条件类。
4-我们使用`on:click`来覆盖事件点击，并调用函数 setActivate 来传递当前选项

## ContentBox 组件

*   请记住，您可以在这里找到该组件的完整源代码:[https://git lab . com/edades/cart-svelte/blob/master/src/Components/content box . svelte](https://gitlab.com/edades/cart-svelte/blob/master/src/Components/ContentBox.svelte)

因此，在这个组件中，我们一开始就有一些样式。

在样式之后，我们有脚本内容。来看看这个:

```
<script>
  import Product from './Product.svelte'
  import { cart } from '../Store/store.js'

  export let products = []

  let total = 0

  cart.subscribe(c => {
    total = c
  })

  const removeItem = () =>
    cart.update(c => c - 1)
</script> 
```

1-导入组件产品
2-导入购物车商店。

-让我们看看商店是如何运作的-

*   在我们项目的文件夹中，我们有 Store/store.js。

```
import { writable } from 'svelte/store'

export const cart = writable(0) 
```

*   这里我们从 svelte/store 导入可写函数，并创建我们的“全局”状态调用`cart`。在这种情况下，我们只有一个带有 int 值的可写。你可以有对象、数组、字符串等等。

-回到内容框组件-

3-这个`export let products = []`是道具接收到的数据。我们也给它分配了一个空数组作为默认值。

4- `let total = 0`是我们存储来自商店的数据的本地状态(购物车状态)

5- `cart.subscribe`是来自购物车对象(商店)的本地函数，它允许购物车状态监听变化并使用这些新数据。
在这种情况下，当购物车状态改变时，我们用新的购物车值设置局部变量 total。

6- `removeItem`是当用户按下从购物车中删除商品的链接时调用的函数。并且这调用一个`cart.update`来用新值更新状态值。在这种情况下，我们采用当前值，并将其减少 1

现在，从 html 端我们有了下面的代码:

```
<div class="content-box">
  <h1>Product list</h1>
  <div class="cart">
    <h3>You have {total} products in the cart</h3>
    {#if total > 0}
      <a href="javascript:void(0)" on:click={removeItem}>Click to remove an item from cart</a>
    {/if}
  </div>
  <div class="product-cotainer">
    {#each products as product}
      <Product product={product} />
    {/each}
  </div>
</div> 
```

1- `<h3>..{total}..</h3>`我们正在显示我们的本地状态`total`，当“全局”状态购物车发生变化时，该值也会发生变化。

2-我们有一个 if 条件来显示/隐藏从购物车中删除商品的链接。

3-我们有一个`each`来动态创建一个`Product`组件，并将一个产品对象作为道具传递给它。

## 产品组件

这里我们没有新的功能。
那么，让我们来快速回顾一下代码。

```
<script>
  import { cart } from '../Store/store.js'
  export let product = {}

  const setCart = () => cart.update(total => total + 1)
</script> 
```

1-导入购物车商店对象
2-从父对象接收产品对象(作为道具)
3- `setCart`更新“全局”状态购物车增加 1。

html 端:

```
<div class="product">
  <picture>
    <img src={product.image} alt="" />
  </picture>
  <div class="product-info">
    <h1>{product.name}</h1>
    <h3>{product.description}</h3>
  </div>
  <div class="product-price">
    <p>{product.price}</p>
    {#if product.stock < 5}
      <strong>¡Hurry up!</strong>
    {/if}
  </div>
  <div class="addtocart">
    <button on:click={setCart}>Add to cart</button>
  </div>
</div> 
```

这里我们可以使用产品对象来获取产品数据。
我们有一个`if`条件来显示/隐藏产品库存数量的消息。
最后，我们有一个带有点击事件的按钮，用于更新购物车状态。

总的来说，这是所有的文件。
记住你可以在这里查看直播网站:[https://silly-liskov-fc7aa8.netlify.com/](https://silly-liskov-fc7aa8.netlify.com/)

____ 在网络上部署 ____

！！首先把你的代码上传到 Gitlab(在这种情况下，但是可以使用 bitbucket 或者 github)。

*   创建一个项目[https://gitlab.com/projects/new](https://gitlab.com/projects/new)
*   上传回购:

```
git clone git@gitlab.com:MYUSER/my-fancy-repo.git
cd my-fancy-repo
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master 
```

* * *

好，现在去[https://www.netlify.com/](https://www.netlify.com/)。
并创建免费账户。

之后，前往[https://app.netlify.com/start](https://app.netlify.com/start)。
并选择你的源代码(gitlab，github，bitbucket)
并最终选择你的 repo (project)

现在，我们需要设置我们的部署设置。

你需要设置分支(这里是 master)
这里的构建命令是:`npm run build`(这个命令在 package.json 文件里)
发布目录是:`public`(这是 svelte 用来存放构建文件的默认文件夹)。

最后，点击“部署网站”:D

现在你已经有了 gitlab 和 netlify 的网站，当你合并代码到主分支(在这种情况下)，netlify 会用你的新变化重新部署你的应用程序，太棒了！！

要获得更多的文档，请访问[https://svelte.dev/](https://svelte.dev/)并查看教程和示例部分。

而对于路线和服务器端功能，svelte 使用 Sapper([https://sapper.svelte.dev/](https://sapper.svelte.dev/))。

这就是全部！见 u；)