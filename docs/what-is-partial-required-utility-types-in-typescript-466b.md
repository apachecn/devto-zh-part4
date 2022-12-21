# TypeScript 中什么是部分和必需的实用程序类型？

> 原文：<https://dev.to/varit05/what-is-partial-required-utility-types-in-typescript-466b>

在真实的应用程序中，使用基本类型，但是在某些情况下，我们需要一些可以从一个或两个类型派生的类型。TypeScript 提供了各种实用工具类型，以便从我们项目中已经定义的类型进行更容易的转换。此外，这些实用程序在全球都可以使用。因此，它可以从任何地方访问。

为了理解下面的概念，需要对 TypeScript 有基本的了解。

***偏`<T>`***

*   创建一个类型，将`T`的所有属性设置为可选。
*   当类型`T`的一些属性被更新时很有用。

```
 interface Todo {
    title: string;
    description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>): Todo {
    return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
    title: 'organize desk',
    description: 'clear clutter',
};

const todo2 = updateTodo(todo1, {
    description: 'throw out trash',
}); 
```

上例中，`<T>`表示需要修改的已定义类型。

***必填`<T>`***

*   创建一个类型，将`T`的所有属性设置为 Required。
*   当所有对象的属性都被设置时有用`T`的所有属性被更新。

```
 interface Todo {
    title: string;
    description?: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Required<Todo>): Todo {
    return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
    title: 'organize desk',
    description: 'clear clutter',
};

const todo2 = updateTodo(todo1, {
    title: 'title updated',
    description: 'throw out trash',
}); 
```

**注意要点**

下面是这两个实用程序可以进行一些调整的场景。

*   当 *strictNullChecks* 标志启用时有用。
*   仅适用于单个级别，不适用于嵌套级别。

奇怪的是，如果`Partial` & `Required`一起使用，最外层的优先级会更高(在理想情况下没有用，但提到它只是为了更好地理解它)。