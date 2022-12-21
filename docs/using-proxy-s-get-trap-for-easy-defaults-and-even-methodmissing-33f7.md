# 使用 Proxy 的 get()陷阱容易造成缺省甚至 method_missing！

> 原文：<https://dev.to/neverendingqs/using-proxy-s-get-trap-for-easy-defaults-and-even-methodmissing-33f7>

[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)的`handler.get()`陷阱很厉害。这里有一些它能做什么的例子。

## 计数器

需要保留一个计数器吗？以下示例显示了列表中每种动物的数量。

```
const animals = [
  'dog', 'cat', 'mouse', 'cat', 'dog',
  'dog', 'mouse', 'cat', 'cat', 'dog',
  'mouse', 'cat', 'mouse', 'dog', 'dog'
];

const counter = new Proxy({}, {
  get: (obj, property) => obj[property] || 0
});

animals.forEach(animal => counter[animal] += 1);

console.log(JSON.stringify(counter, null, 2)); 
```

```
{  "dog":  6,  "cat":  5,  "mouse":  4  } 
```

## 数组

或者在一个数组对象中把东西组合在一起怎么样？

```
const data = {
  posts: [
    { name: 'post1', tags: ['tag1', 'tag2', 'tag3'] },
    { name: 'post2', tags: ['tag2', 'tag5', 'tag7'] },
    { name: 'post3', tags: ['tag3', 'tag6', 'tag7'] },
    { name: 'post3', tags: ['tag2', 'tag3', 'tag6'] },
    { name: 'post3', tags: ['tag1', 'tag4', 'tag5'] }
  ]
};

const postsByTag = new Proxy({}, {
  get: (obj, property) => obj[property] || []
});

data.posts.forEach(post =>
  post.tags.forEach(tag =>
    postsByTag[tag] = postsByTag[tag].concat(post.name)
  )
);

console.log(JSON.stringify(postsByTag, null, 2)); 
```

```
{  "tag1":  [  "post1",  "post3"  ],  "tag2":  [  "post1",  "post2",  "post3"  ],  "tag3":  [  "post1",  "post3",  "post3"  ],  "tag5":  [  "post2",  "post3"  ],  "tag7":  [  "post2",  "post3"  ],  "tag6":  [  "post3",  "post3"  ],  "tag4":  [  "post3"  ]  } 
```

## 红宝石的`method_missing`

你甚至可以模仿 Ruby 的 [method_missing](https://ruby-doc.org/core-2.6.3/BasicObject.html#method-i-method_missing) 在飞行中做决定！

```
const greeter = new Proxy({}, {
  get: (obj, property) => {
    if(property.startsWith('print')) {
      const toPrint = property.split('_').slice(1);
      return () => toPrint.forEach(p => console.log(obj[p]));
    } else {
      return obj[property];
    }
  }
});

greeter.firstName = 'Jane';
greeter.lastName = 'Doe';
greeter.helloWorld = () => console.log('Hello World');

greeter.helloWorld();
greeter.print_firstName_lastName(); 
```

```
Hello World
Jane
Doe 
```

这些只是`handler.get()`陷阱的一小部分例子。你对`Proxy`对象做过什么？