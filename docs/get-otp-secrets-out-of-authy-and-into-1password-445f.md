# 将 OTP 机密从 Authy 中取出并放入 1password 中

> 原文：<https://dev.to/davidsauro/get-otp-secrets-out-of-authy-and-into-1password-445f>

一段时间以来，我一直试图找出如何从 Authy(一次性代码生成器)中提取我的“秘密”,并将其导入 1password。

在谷歌上搜索了一下，我找到了 Guillaume Boudreau 的一篇文章，这篇文章让我达到了 99%的目标。

[https://gist . github . com/gboudreau/94 bb 0 c 11 a 6209 c 82418d 01 a 59d 958 c 93](https://gist.github.com/gboudreau/94bb0c11a6209c82418d01a59d958c93)

按照他的指示执行 javascript 代码后，我注意到“period”被硬编码为 10 秒，并且不适用于标准的 Google Auth 代码，只适用于 Authy 特定的代码。

在逐步完成代码后，很明显我可以修改句点来生成正确的 6 位数代码。

我正要写一些条件逻辑，注意到约翰·查德威克/玉莲·昆切夫在评论中得出了相同的结论，所以我窃取了他们的剪报，并将其转储到纪尧姆发布的源代码中。

我执行了 js 并运行了一些测试，它工作得很好。