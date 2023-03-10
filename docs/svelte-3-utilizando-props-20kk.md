# 苗条 3:利用道具

> 原文：<https://dev.to/oieduardorabelo/svelte-3-utilizando-props-20kk>

## 网络增强的 web 应用

> **props** 在 React 和 vista . js 等其他框架中流行，是允许组件通信的非常有效的方法。 **props** 按您预期的方式快速运行。它们从父代组件传递到子代，用于指定组件可以使用的数据，以输入 DOM 中处理的内容。

让我们示范一下如何通过构造名为**的简单元件来使用**:T3】****

```
<script>
  export let title;
  export let description;
  export let imageUrl;
</script>

<style>
  h1 {
    color: coral;
  }
  section {
    border-radius: 8px;
    box-shadow: 0 0 4px rgba(255, 0, 0, 0.1);
    max-width: 600px;
    margin: 1rem auto;
    padding: 1rem 2rem;
  }
  img {
    width: 32px;
    height: 32px;
    margin-right: 12px;
    vertical-align: middle;
  }
</style>

<section>
  <h1>
    <img src={imageUrl} alt="Avatar for {title}" />
     {title}
  </h1>

  <p>{description}</p>
</section> 
```

如您所见，您可以使用 ES6 的“`export`”语法快速通知组件所支持的 props。然后可以通过简单的值插值在组件中使用 props。

使用该组件时，可以提供诸如:
等 props

```
<script>
  import Card from "./Card.svelte";
</script>

<Card
  title="See you later, Alligator"
  imageUrl="https://alligator.img/alligator-logo3.svg"
  description="Not so soon, baboon!" /> 
```

## 可选 props 的默认值

按照我们目前的配置，所有 props 都是强制性的，如果我们不传递任何值，则 fast 将在控制台中发出警告:

```
<script>
  import Card from "./Card.svelte";
</script>

<Card
  title="See you later, Alligator"
  description="Not so soon, baboon!" /> 
```

我们会得到警告:

```
<Card> was created without expected prop 'imageUrl'. 
```

为了纠正这种情况，我们可以使用类似:
的东西，为任何声明的属性提供默认值

```
<script>
  export let title;
  export let description = "Description coming soon!";
  export let imageUrl = "https://picsum.photos/64";
</script>

<style>
  /* ... */
</style>

<section>
  <h1>
    <img src={imageUrl} alt="Avatar for {title}" />
     {title}
  </h1>

  <p>{description}</p>
</section> 
```

而现在您可以使用组件`Card`而无需传递`description`或`imageUrl`，该组件将使用默认值:

```
<script>
  import Card from "./Card.svelte";
</script>

<Card
  title="Tood-a-loo, Kangaroo!" /> 
```

## 使用 props 中的扩频

与[在 jsx](https://alligator.io/react/jsx-introduction/#spreading-props)中传播 props 类似，fast 允许您从代码中的对象传播 props，以避免额外键入。

这里有一个例子，我们把蜂鸣器展开，把它与长表单组件中的输入内容进行比较:

```
<script>
  import Card from "./Card.svelte";

  const items = [
    {
      id: 1
      title: "Pirate",
      description: "Argg!!",
      imageUrl: "https://alligator.img/pirate.svg"
    },
    {
      id: 2
      title: "Chef",
      description: "À la soupe!",
      imageUrl: "https://alligator.img/chef.svg"
    }
  ];
</script>

<!-- Sem propagação: -->
{#each items as item}
  <Card
    title={item.title}
    description={item.description}
    imageUrl={item.imageUrl}
  />
{/each}

<!-- Com propagação: -->
{#each items as item}
  <Card {...item} />
{/each} 
```

注意，当我们把物体的蜂窝状撒在`id`上时，额外的属性(`id`也传递出去，这并不重要。组件`Card`并未声明使用`id`作为属性，因此被忽略。

> 如上例所示，我也在我的标记内使用逻辑块`#each`。我将在以后的帖子中报道速递所提供的逻辑组件，但您也可以在《速递官方教程》中读到这方面的全部内容。

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [配合 fast 使用 props】，原由](https://alligator.io/svelte/using-props/)[@鳄鱼](https://mobile.twitter.com/alligatorio)**