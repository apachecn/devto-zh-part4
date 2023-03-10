# 如何使用 JavaScript bcrypt 库

> 原文：<https://dev.to/baransel/how-to-use-the-javascript-bcrypt-library-4iok>

bcrypt npm 包是 JavaScript 中使用密码最多的包之一。

这是安全 101，但对新开发人员来说值得一提:您永远不会将密码以纯文本的形式存储在数据库或任何其他地方。你就是不知道。

你要做的是，从密码中生成一个散列，并存储它。

这样:

```
import bcrypt from 'bcrypt'
// or
// const bcrypt = require('bcrypt')

const password = 'oe3im3io2r3o2'
const rounds = 10

bcrypt.hash(password, rounds, (err, hash) => {
    if (err) {
    console.error(err)
    return
  }
  console.log(hash)
}) 
```

Enter fullscreen mode Exit fullscreen mode

你传递一个数字作为第二个参数，这个数字越大，散列就越安全。而且产生它的时间也越长。

该库的自述文件告诉我们，在 2GHz 内核上，我们可以生成:

```
rounds=8 : ~40 hashes/sec
rounds=9 : ~20 hashes/sec
rounds=10: ~10 hashes/sec
rounds=11: ~5  hashes/sec
rounds=12: 2-3 hashes/sec
rounds=13: ~1 sec/hash
rounds=14: ~1.5 sec/hash
rounds=15: ~3 sec/hash
rounds=25: ~1 hour/hash
rounds=31: 2-3 days/hash 
```

Enter fullscreen mode Exit fullscreen mode

如果多次运行`bcrypt.hash()`，结果会不断变化。这很关键，因为没有办法从哈希中重建原始密码。

给定相同的密码和一个散列，使用`bcrypt.compare()`函数:
可以发现散列是否是从该密码构建的

```
bcrypt.compare(password, hash, (err, res) => {
  if (err) {
    console.error(err)
    return
  }
  console.log(res) //true or false
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果是这样，密码匹配散列，例如，我们可以让用户成功登录。

您也可以使用`bcrypt`库及其基于 promise 的 API，而不是回调:

```
const hashPassword = async () => {
  const hash = await bcrypt.hash(password, rounds)
  console.log(hash)
  console.log(await bcrypt.compare(password, hash))
}

hashPassword() 
```

Enter fullscreen mode Exit fullscreen mode