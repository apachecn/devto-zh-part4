# Shamir 的 JavaScript 秘密共享方案

> 原文：<https://dev.to/simbo1905/shamir-s-secret-sharing-scheme-in-javascript-2o3g>

密码是安全的克星，所以它们必须是强有力的，永远不要重复使用。开发人员同意最后一句话，那么就不要给他们的用户提供安全备份强密码的方法。我们应该为用户提供使用 Shamir 的秘密共享方案恢复强密码的能力。用户可以放心地使用一个独特的强密码，知道他们不会被锁定。

沙米尔的秘密共享方案到底是什么？这是一种秘密分割的形式，我们将密码作为一组份额分发。只有当足够阈值的份额被重新组合在一起时，才能重构原始密码。下面是使用 [shamir](https://www.npmjs.com/package/shamir) 库:
展示这是如何工作的示例代码

```
const { split, join } = require('shamir');
const { randomBytes } = require('crypto');

// the total number of shares
const PARTS = 5;
// the minimum required to recover
const QUORUM = 3;
// you can use any polyfill to covert between string and Uint8Array
const utf8Encoder = new TextEncoder();
const utf8Decoder = new TextDecoder();

function doIt() {
    const secret = 'hello there';
    const secretBytes = utf8Encoder.encode(secret);
    // parts is a object whos keys are the part number and 
    // values are shares of type Uint8Array
    const parts = split(randomBytes, PARTS, QUORUM, secretBytes);
    // we only need QUORUM parts to recover the secret
    // to prove this we will delete two parts
    delete parts[2];
    delete parts[3];
    // we can join three parts to recover the original Unit8Array
    const recovered = join(parts);
    // prints 'hello there'
    console.log(utf8Decoder.decode(recovered));
} 
```

加密货币钱包使用 Shamir 的秘密共享，使用户能够备份他们的密码。这解决了一个问题，如果有人去世，比特币可以传给朋友和家人。你会如何使用这种方法来保护价值一千万美元的比特币密码？您可以生成五个份额，并将阈值设置为三。然后，你可以将两份股票送给两个信任的朋友，在纸上写下两份股票，然后将它们存放在单独的安全位置，并将最后一份股票交给你的律师。那么其他人就很难获得三份来窃取你的比特币。你最后的遗嘱文件可以说明如果你死了如何找回比特币。

难道不是时候让你的应用程序强制执行一个强密码，并让人们选择使用 Shamir 的秘密共享方案来备份它了吗？