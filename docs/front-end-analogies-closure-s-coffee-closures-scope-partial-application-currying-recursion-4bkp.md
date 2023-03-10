# 前端类比:闭包的咖啡——闭包，词法范围

> 原文：<https://dev.to/recss/front-end-analogies-closure-s-coffee-closures-scope-partial-application-currying-recursion-4bkp>

## 封氏咖啡

#### 哪里的咖啡适合封口

今天我们要参观的是一家国际特许连锁咖啡馆，专门供应各种咖啡。我们将跳到几个区域和角色，以便更好地了解这个地方。

这种氛围绝对是博士生在他们的 MacBooks 上工作，而穿着制服、少了 20 美元的博士生在把豆子煮成精力充沛的浆糊。就像其他咖啡馆一样。尽量不要踩到自己的鞋子，好吗？

* * *

### 来自企业的拜访

#### 词法范围，继承，隐私

总部的一些无生气的外壳正在建立两个新的商店。得告诉他们规则什么的。没有纹身，没有人打电话，什么都没有。然后西装革履的人会为下一家店复制他们的作品。

第二家店有人写新闻稿。公司想提醒他们公司名称总是用大写字母。太好了，现在我们开始大喊大叫了。

哦，是的，那些孩子真的很喜欢他们的掌上电脑。只是要偷偷摸摸一点。

```
 "use strict";

const corp_storeOneRules = {
    tattoosVisible: false,
    phoneAtRegister: false,
    pettyAnnoyance: true
};

const corp_storeTwoRules = {
    ...corp_storeOneRules,
    // Run it back.
    // Why half-ass two things when you can whole-ass one thing, repeatedly?
    brandNameCaps: _brandName => _brandName.toUpperCase()
};

console.log(corp_storeOneRules);
/*
Object {
    pettyAnnoyance: true,
    phoneAtRegister: false,
    tattoosVisible: false
}
*/

console.log(corp_storeTwoRules);
/*
Object {
    brandNameCaps: _brandName => _brandName.toUpperCase(),
    pettyAnnoyance: true,
    phoneAtRegister: false,
    tattoosVisible: false
}
*/

// Store two has rules nearly identical to store one,
// but with the rule about branding added.

// console.log(corp_storeOneRules.brandNameCaps("closure's coffee"));

// Come in the store talking about formatting press announcements
// and people will be looking at you studid.

console.log(corp_storeTwoRules.brandNameCaps("closure's coffee"));

const employees_storeRules = {
    phoneAtRegister: true,
    phoneAtRegisterWithCorp: false
};
// Looks like the workers aren't going down without a fight.

const corp_giveStoreRules = _rules => {
    const secretRules = "Fuggedaboutit.";
    // You couldn't change this if you wanted to.
    // Well, maybe with an obscene amount of money.
    // That can do anything.

    const existingRules = _ourRules => {
        return {
            ..._rules,
            ..._ourRules,
            secret: secretRules
            // If you don't know, that's called the "spread operator".
        }
    }

    return existingRules;
};

console.log( corp_giveStoreRules(corp_storeOneRules)(employees_storeRules) );
/*
Object {
    pettyAnnoyance: true,
    phoneAtRegister: true,
    phoneAtRegisterWithCorp: false,
    secret: "Fuggedaboutit.",
    tattoosVisible: false
}
*/

console.log( corp_giveStoreRules(corp_storeTwoRules)(employees_storeRules) );
/*
Object {
    brandNameCaps: _brandName => _brandName.toUpperCase(),
    pettyAnnoyance: true,
    phoneAtRegister: true,
    phoneAtRegisterWithCorp: false,
    secret: "Fuggedaboutit.",
    tattoosVisible: false
}
*/

// Though we couldn't change or directly access the secret rules,
// we still have to worry about them. Well, they do.

// You know, no one ever wonders where the .toUpperCase() comes from.
// Thing is: it fell off a truck. That's how we inherit around here. 
```