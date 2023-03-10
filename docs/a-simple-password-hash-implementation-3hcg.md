# 一个简单的密码散列实现

> 原文：<https://dev.to/nedsoft/a-simple-password-hash-implementation-3hcg>

出于明显的安全原因，密码不会以纯文本形式存储。已经有几个用于密码加密的 npm 包，如 [bcrypt.js](https://www.npmjs.com/package/bcrypt) 、[密码哈希](https://www.npmjs.com/package/password-hash)等。

本文的目的并不是为现有库已经提供的解决方案提供一个更好的解决方案，而是揭示实现是如何工作的。

## 入门

我假设您已经创建了一个项目。现在创建一个名为`custom-bcrypt.js`的文件。

将下面的代码片段复制到`custom-bcrypt.js`文件:

```
const md5 = require("md5");

module.exports = {
  /**
   *
   *
   * @param { string } rawPass - the password to be hashed
   * @param { object } [options={}] - object containing salt and rounds
   * @returns {string} 
   */
  hash(rawPassword, options = {}) {
    /**
     * salt is optional, if not provided it will be set to current timestamp
     */
    const salt = options.salt ? options.salt : new Date().getTime();

    /**
     * rounds is optional, if not provided it will be set to 10
     */
    const rounds = options.rounds ? options.rounds : 10;

    let hashed = md5(rawPassword + salt);
    for (let i = 0; i <= rounds; i++) {
      hashed = md5(hashed);
    }
    return `${salt}$${rounds}$${hashed}`;
  },
  /**
   *
   *
   * @param {string} rawPassword - the raw password
   * @param { string } hashedPassword - the hashed password
   * @returns
   */
  compare(rawPassword, hashedPassword) {
    try {
      const [ salt, rounds ] = hashedPassword.split('$');
      const hashedRawPassword = this.hash(rawPassword, { salt, rounds });
      return hashedPassword === hashedRawPassword;
    } catch (error) {
      throw Error(error.message);
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们解释一下上面的代码片段是怎么回事。

首先，注意我们需要`md5`库，它是哈希的基础。现在，在项目中安装`md5`。

```
npm add md5 or yarn add md5 
```

Enter fullscreen mode Exit fullscreen mode

### 盐

在密码学中，salt 是随机数据，用作单向函数的附加输入，该单向函数“散列”数据、密码或密码短语。盐是用来保护密码的。Salts 抵御预先计算的散列攻击。——[维基百科](https://en.wikipedia.org/wiki/Salt_(cryptography))

### 回合

轮次指定哈希中使用的迭代次数。回合越高，黑客就越难使用[彩虹表](https://whatis.techtarget.com/definition/rainbow-table)猜出密码。

注意，在上面的`custom-bcrypt`模块中，我们有两个函数`hash`和`compare`。

### 哈希函数

`hash`函数有两个参数，要散列的密码和默认设置为空对象的`options`对象。`options`对象有两个可选属性`salt`和`rounds`，分别设置为`current timestamp`和`10`。该函数使用 md5 对密码加`salt`加密，次数与`rounds`一样多。返回值是一个由`salt`、`rounds`和`hashed value`连接在一起的字符串。

### 比较功能

`compare`函数有两个参数，要验证的原始密码和之前散列的密码。它从先前散列的密码中提取出`salt`和`rounds`，然后用它来散列当前的原始密码，并返回一个对应的布尔值来判断密码是否匹配。

现在，让我们测试我们的自定义 bcrypt 模块。创建一个名为`sample.js`的文件。
将下面的代码复制到`sample.js`到

```
const bcrypt = require('./custom-bcrypt')

const rawPassword = 'password'

console.log(bcrypt.hash(rawPassword))
//1563995248971$10$58e0867f3acc11de363e03389bb27167

console.log(bcrypt.compare('password','1563995248971$10$58e0867f3acc11de363e03389bb27167'));
//true

console.log(bcrypt.hash(rawPassword, {salt: 'someRandomString', rounds: 20}))
//someRandomString$20$199d9de71859a87cdd22e52d93f4522a

console.log(bcrypt.compare('password', 'someRandomString$20$199d9de71859a87cdd22e52d93f4522a'));
//true 
```

Enter fullscreen mode Exit fullscreen mode

您可以按照自己的意愿测试它，为了这篇文章，我在终端上使用`node sample.js`测试了它。

> 免责声明:本文不保证这里实现的加密的安全性。

## 结论

在本文中，我们试图阐明密码加密是如何工作的。如果您对本文有任何问题或贡献，请随时联系我。✌️

*本文最初发表在我的[博客](https://www.oriechinedu.com/posts/simple-password-hash-implementation/)T3 上*