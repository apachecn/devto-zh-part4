# 凯撒密码+ 1

> 原文：<https://dev.to/funcelot/caesar-cipher-1-4bf2>

精确变化的总数等于 88！* 1000 * 2147483647 = 3.983209e+146，大部分都略有不同。

凯撒密码+1 *优点*:

1.  输入的 N 个误差产生输出的 *N* 个误差
2.  密码对输入字母中的错误不敏感
3.  密码输出严重依赖静脉注射

凯撒密码+1 *缺点*:

1.  签名算法的运气
2.  小 IV /容易产生力
3.  抵御差分攻击能力弱

它将支持 RC 错误纠正，并且可以与数字签名结合使用(需要 DC 来防止微小的改变)

[https://codepen.io/root_admin/embed/PoYzazw?height=600&default-tab=result&embed-version=2](https://codepen.io/root_admin/embed/PoYzazw?height=600&default-tab=result&embed-version=2)