# Working with conditions & lists in React

> 原文：[https://dev.to/sergiodxa/working-with-conditions-list-in-react-1g01](https://dev.to/sergiodxa/working-with-conditions-list-in-react-1g01)

In a previous article I wrote about [how to start a React project using the state and effect hooks](https://sergiodxa.com/articles/react-state-effects), this time we will see how to work with conditions, lists and forms in React.

### Conditional Rendering in React

Let's start talking about conditional rendering, multiple times you will need to render something in React based on a condition and if the condition is `false` render a different thing. To do so we have multiple options.

#### Using a Normal `if`

The easiest way is to use a normal `if` inside our component code and return inside and outside the `if`.

```
const Spinner = () => <strong>Loading...</strong>; 
function MyComponent({ isLoading }) {
  if (isLoading) return <Spinner />;
  return <main>This is the real content</main>; } 
```

Enter fullscreen mode Exit fullscreen mode

In our component if `isLoading` is true we will return the component `<Spinner />` but if `isLoading` is false we will render the `<main>` with some text inside.

#### Switching Only Content Using Ternaries

Another option is to use ternaries directly inside the returned elements. This is specially useful if you want to render some content always and only do the changes in a specific area.

```
function MyComponent({ isActive }) {
  return (
    <main>
      I'm <strong>{isActive ? "active" : "inactive"}</strong>
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

In this case it will return the `<main>` and the `I'm` all the time, inside a strong it will return `active` or `inactive` based on the condition, if we don't use JSX we need to return those values as strings inside quotes instead of the text directly, this is because inside the curly brackets we use normal JavaScript code and not JSX.

#### Switching Elements Tree Using Ternaries

We could also use ternaries to render different elements based on conditions.

```
const Spinner = () => <strong>Loading...</strong>; 
function MyComponent({ isLoading }) {
  return (
    <main>
      <h2>This is my app</h2>
      {isLoading ? <Spinner /> : <article>This is the real content</article>}
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

This is similar to the examples above, we will always have the `main` and `h2` tags but we will render the `<Spinner />` if `isLoading` is `true` and and `article` with certain content if `isLoading` is `false`. Unlike the previous example we don't need to wrap the content in quotes because we wrote it inside JSX code so we recover that capability as in the `h2`.

#### Rendering an Element or Null

There is also another possible case where you need to render an element or nothing, again there are a few options.

##### Using a Ternary

The first one is using a ternary to render the element or `null`.

```
const Spinner = () => <strong>Loading...</strong>; 
function MyComponent({ isLoading }) {
  return (
    <main>
      <h2>This is my app</h2>
      {isLoading ? <Spinner /> : null}
      <article>This is the real content</article>
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

In this case if `isLoading` is `true` it will render the `<Spinner />` but if it's `false` it will render `null`. If we use `null` inside our JSX React will ignore it and avoid rendering in the position.

##### Using an Implicit Null

We could leverage JavaScript to have implicit `null` in our JSX, technically is a `false` which will also be ignored by React but is similar to the example above.

```
const Spinner = () => <strong>Loading...</strong>; 
function MyComponent({ isLoading }) {
  return (
    <main>
      <h2>This is my app</h2>
      {isLoading && <Spinner />}
      <article>This is the real content</article>
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

If `isLoading` is `true` JavaScript will execute the next part of the condition where we return `<Spinner />`, but if `isLoading` is `false` it will stop the condition there and return `false`, the result is similar to the example above but we don't need to use a `null` there.

### Lists in React

Now let's talk about working with list, if we have an array of element we could use [Array#map](https://sergiodxa.com/articles/js-basics/array-prototype-map) to transform the elements to React elements, before such transformation we could use other array methods such as [Array#filter](https://sergiodxa.com/articles/js-basics/array-prototype-filter), Array#sort, etc. As far they return a new array we could keep adding more and eventually map to React elements.

```
function List() {
  const messages = [
    { content: "Lorem", id: 1 },
    { content: "Ipsum", id: 2 },
    { content: "dolor", id: 3 },
    { content: "Sit", id: 4 },
    { content: "Amet", id: 5 }
  ];

  return (
    <ul>
      {messages
        .filter(({ content }) => content[0] !== content[0].toLowerCase())
        .map(message => (
          <li key={message.id}>{message.content}</li>
        ))}
    </ul>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

If we review the example above it's possible to see the prop `key` which is equal to each `message.id`, this `key` is not an attribute of the `li` tag, it is something we need to define when rendering lists on React.

This key lets React identify each item of the list by some **immutable** value, the immutable part is specially important, the value should never change and if it does React will treat it as a different element and force a new render.

In case our list is filtered or sorted the key will help React identify each element in their new positions and reduce the DOM updates to the minimum.

> *Tip*: Avoid using the `index` as key, while it works if the list is filtered or sorted it will cause the key value to change to the new index and React will consider them different elements and repaint the whole list.