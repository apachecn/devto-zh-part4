# 用 JavaScript 设计 API 方法

> 原文：<https://dev.to/jsmanifest/designing-api-methods-in-javascript-1n30>

[![designing api methods in javascript](img/6de1942f391de7f5a3026b7b5076a644.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a8aOoXNM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/designing-api-methods-in-javascript/thumbnail.jpg)

# 探索命名参数、方法链接和函数多态性

用 JavaScript 设计 API 方法是一项非常有用的技能，可以让你从不同的角度看待编程。而不是为你的用户构建一个项目，你是在为开发者构建一个项目。如果您以前没有构建过库或 SDK，本文可能会帮助您了解从哪里以及如何开始方法设计。

JavaScript 是一种松散类型的语言，我们作为开发人员可以利用它来创建健壮的、多用途的 API。

本文将介绍在用 JavaScript 为 API 设计方法时需要记住的一些规则。

## 1。命名参数

我们要讲的第一个是命名参数。早在 ES6 引入之前，声明函数的唯一方式是使用`function`语法的[函数声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)。要确定执行流的去向，您需要一个函数 arity(函数期望的参数数量)，将它转换为一个数组，并根据参数的外观应用其余的逻辑。

在这个例子中，`animal`、`options`和`callback`是函数的参数，arity 将是三。该功能旨在创建一个新帐户，如果不是呼叫者提供的，每个帐户都会有一些默认设置:

```
function createAccount(
  username = '',
  password = '',
  nickname = '',
  email = '',
  gender = 'Male',
  bio = '',
  subscription = 'Basic',
  callback,
) {
  if (!username || !password || !email) {
    throw new Error(
      'You are missing one or all of the following fields: "username", "password", "email"',
    )
  }
  return api
    .createAccount({
      username,
      password,
      nickname,
      email,
      gender,
      bio,
      subscription,
    })
    .then((result) => {
      if (callback) callback(null, result)
    })
    .catch((error) => {
      console.error(error)
      if (callback) callback(error)
    })
}

createAccount(
  'lucas',
  'applebee123x123',
  '',
  'applebee1233@gmail.com',
  '',
  'My bio',
  'Basic',
  function cb(err, data) {
    if (err) {
      console.error(err)
    }
    // do something with data
  },
) 
```

Enter fullscreen mode Exit fullscreen mode

这样做的问题是，调用者必须知道要作为参数传递给函数的参数的确切顺序，以便即使不需要一个或多个参数也能正常工作。按顺序记住需求可能很难，但如果不小心的话，很容易把顺序弄乱。此外，如果一个参数不是函数正常工作所必需的，那么让它成为必需参数就没有多大意义。

将来也很难维护，因为当你或你的老板需要去掉`username`并把它作为新的用户名`email`时，你必须改变逻辑。

更好的做法是简单地使用一个对象:

```
function createAccount({
  username = '',
  password = '',
  nickname = '',
  email = '',
  gender = 'Male',
  bio = '',
  subscription = 'Basic',
  callback,
}) {
  if (!username || !password || !email) {
    throw new Error(
      'You are missing one or all of the following fields: "username", "password", "email"',
    )
  }
  return api
    .createAccount({
      username,
      password,
      nickname,
      email,
      gender,
      bio,
      subscription,
    })
    .then((result) => {
      if (callback) callback(null, result)
    })
    .catch((error) => {
      console.error(error)
      if (callback) callback(error)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们受益于可读性以及对可维护性的更多控制，因为您只需要从代码中删除用户名:

```
function createAccount({
  password = '',
  nickname = '',
  email = '',
  gender = 'Male',
  bio = '',
  subscription = 'Basic',
  callback,
}) {
  if (!password || !email) {
    throw new Error(
      'You are missing one or all of the following fields: "email", "password"',
    )
  }
  return api
    .createAccount({
      password,
      nickname,
      email,
      gender,
      bio,
      subscription,
    })
    .then((result) => {
      if (callback) callback(null, result)
    })
    .catch((error) => {
      console.error(error)
      if (callback) callback(error)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

打电话也变得更加简洁易读:

```
createAccount({
  password: 'applebee123x123',
  email: 'applebee1233@gmail.com',
  bio: 'My bio',
  callback: function cb(err, data) {
    if (err) {
      console.error(err)
    }
    // do something with data
  },
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 2。方法链接/流畅 API

我最喜欢的编写 API 的方式是通过方法链接使用 [fluent API](https://martinfowler.com/bliki/FluentInterface.html) 。

方法链接就是将多个调用一个接一个地链接起来的过程。总的想法是实现一个可读和流畅的代码，从而使它更快地理解。这些方法通常是动词(如 rotate)

例如:

```
getPhoto('../nemo_the_fish.jpg')
  .applyFilter('grayscale', '100%')
  .rotate(100)
  .scale(1.5) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着:“检索图像 *nemo_the_fish.jpg* 并应用值为 100%的灰度滤镜，将图像旋转 100 度，并将比例增加 1.5 倍。”

这种做法的一个好处是可以很快开始编写自己的流畅 API 接口。您只需在方法调用中返回对上下文的引用，这样它就可以被链接:

```
const createWarrior = function createWarrior(name) {
  let hp = 100
  let battleCryInterval = 0

  return {
    bash: function(target) {
      target -= 10
      return this
    },
    // Increase the wrarior's health by 60, decrementing it by 1 every second for 60 seconds
    battleCry: function battleCry() {
      hp += 60
      battleCryInterval = setInterval(() => {
        hp -= 1
      }, 1000)
      setTimeout(() => {
        if (battleCryInterval) {
          clearInterval(battleCryInterval)
        }
      }, 60000)
      return this
    },
    getHp: function getHp() {
      return hp
    },
  }
}

const warrior = createWarrior('chris')
const otherWarrior = createWarrior('bob')

warrior
  .battleCry()
  .bash(otherWarrior)
  .bash(otherWarrior)
  .bash(otherWarrior)
  .bash(otherWarrior)
  .bash(otherWarrior)

const otherWarriorsHp = otherWarrior.getHp()

console.log(otherWarriorsHp) // result: 100 
```

Enter fullscreen mode Exit fullscreen mode

流畅 API 的一个最好的例子是 jQuery，由于库的流畅，它可以说是最容易学习和使用的 JavaScript 库之一:

```
$(window).resize(function() {
  $('#logbox').append('<div>The window resized</div>')
}) 
```

Enter fullscreen mode Exit fullscreen mode

然而，链接 fluent API 的方法有一些缺点。

最大的缺点是很难在链的中间设置断点，这使得错误很难调试。此外，它鼓励过多的程序代码。通过在链中的任何一步插入记录器，并使用它们来调用后续方法，有一些方法可以解决调试问题。

另一个缺点是，您可能会陷入用紧凑的点标记法写长句的行为中。这可能会妨碍事情的简单，所以记住要保持事情的简单。

## 3。功能多态性

你可能听说过其他语言中的术语*多态性*，通常是指基于上下文的不同行为。

同样的概念也适用于 JavaScript 中的函数多态性。这些函数根据传入的参数(也就是我们的*上下文*)来运行。

API 通常将参数收集到数组或类似数组的结构中，以便对它们进行更多的控制。将它们放在一个数组结构中允许它们做一些事情，比如将它们传递给同一作用域中的其他函数，反之亦然。

在引入 arrow 函数之前，在多态函数中收集参数的常见做法是使用类似数组的`arguments`对象。有时，在将参数分配给数组后，您可能需要对它们做更多的事情。尽管 arguments 是一个类似数组的对象，但它的功能并不像一个真正的数组，因为它缺少基本的数组函数——这是非常有限的。

开发人员解决这个问题的方法是使用`Array.prototype.slice.call()`制作一个单独的浅层副本。这叫`method delegation`。换句话说，你*委托*对`Array.prototype`对象进行 slice()调用。

这样的一个例子应该是这样的:

```
const args = Array.prototype.slice.call(arguments, 0) 
```

Enter fullscreen mode Exit fullscreen mode

这将从索引 0 开始复制项目，并返回所有内容。

*参数*没有像`.push`或`.shift`那样的真正方法，所以我们用`Array.prototype.slice`将其转换成一个数组，这样我们就可以访问所有的数组方法。

在 ES6 中，我们可以通过使用如下所示的 spread 操作符将它转换成一个数组:

```
const someFunction = function(...args) {
  console.log(args)
  console.log(args.shift())
}

someFunction(1, 'hello', 'bob')
// result:
// [1, "hello", "bob"]
// 1 
```

Enter fullscreen mode Exit fullscreen mode

当您将参数放入数组或类似数组的结构中时，您可以根据参数的外观来确定执行逻辑的位置。这使得它可以非常灵活地用于多种目的，而无需编写太多代码。

不扩散:

```
const applyFilter = function(filter, value, options) => {
  const args = [].slice.call(arguments, 0)
  console.log(args.length) // result: 2
}

applyFilter('grayscale', '100%') 
```

Enter fullscreen mode Exit fullscreen mode

带展开:

```
const applyFilter = (...args) => {
  console.log(args.length) // result: 1
}

applyFilter('grayscale', '100%') 
```

Enter fullscreen mode Exit fullscreen mode

记住这一点，我们现在可以从这些参数中确定如何处理执行:

```
const applyFilterToImage = (image) => {
  return function applyFilter(...args) => {
  // we can also grab args with [].prototype.slice.call(arguments, 0)

    let options
    let filters = {}
    let callback

    const arg1 = args[0]
    // The caller wants to apply multiple filters
    if (args.length === 1) {
      if (arg1 && typeof arg1 === 'object') {
        filters = { ...arg1 }
        // Find out of the caller wants the new image with applied filters back by checking if a callback was passed in
        const arg2 = args[1]
        if (arg2 && typeof arg2 === 'function') {
          callback = arg2
        }
      } else {
        throw new Error(
          'You must supply an object if you are only providing the first argument',
        )
      }
    } else {
      if (args.length > 2) {
        // The caller passed in options as the third argument
        if (typeof args[3] === 'object') {
          options = args[3]
        }
          // The caller provided a callback function and wants the image with applied filters passed back
        else if (typeof args[3] === 'function') {
          callback = args[3]
        }
      }
      // The caller wants to apply one filter
      if (typeof arg1 === 'string') {
        const filter = arg1
        const value = args[1]
        filters[filter] = value // or filters = { [filter]: value }
      } else {
        if (callback) {
          callback(new Error('Filter is not a string'))
        }
      }
    }
      const newImg = api.filterImage(filters, options)
      if (callback) {
        return callback(null, newImg)
      }
  }
}

const img = '../bob_the_builder.jpg'
const applyFilter = applyFilterToImage(img)
const callback = (newImg) => {
  console.log(newImg)
}

applyFilter({
  grayscale: '100%',
  rotate: 100,
  scale: 1.5,
}, callback) 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的函数允许开发者以多种方式使用它:

1.  通过提供一个字符串和值作为第一个和第二个参数来应用单个筛选器。
2.  或者通过提供一个键/值对对象作为第一个参数来应用多个过滤器。
3.  或者通过提供一个过滤器键/值的对象作为第一个参数来应用单个过滤器。
4.  可选地通过回调接收新过滤的图像作为第二个或第三个参数(位置取决于它们为过滤器传递的内容。)

## 结论

本文到此结束。以后请留意我的更多帖子！