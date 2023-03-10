# PowerShell 核心|更新帮助|获取帮助| CentOS

> 原文：<https://dev.to/ewertonjordao/powershell-core-update-help-get-help-centos-5h7a>

在 Linux 上使用 PowerShell 帮助

<figure>[![](img/ca00084f19d0165f4fe6c59a55a1c961.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m9zRmY-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyBLUGiGgK4FBVu-_7jSzPA.jpeg)

<figcaption>【powershell core+tux 图像。ewton jordao</figcaption>

</figure>

你们好吗？我将演示如何在 PowerShell 中获得帮助，逐步操作适用于 PowerShell 支持的所有操作系统，但在本演示中，我处于 Linux(CentOS)环境中。

PowerShell 中的帮助对于了解 cmdlet 所做的工作而无需在 internet 上查询非常有用，通过帮助，我可以了解到必须为 cmdlet 的运行输入哪些参数，以及哪些数据类型支持哪些参数。

要访问脱机帮助，我们需要运行 Update-Help 命令此命令下载 cmdlets 文档。

[![](img/1cc51f4e7d913bd2c785278559f0250c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vtbphx5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMDGwTNbmsOj7Sq6OVoHzVA.png) 

<figcaption>运行 Update-Help</figcaption>

命令

注意:更新命令正在进行一系列下载，但无法在打印时捕获

下载完成后，我们现在实际上要用 Get-Help <cmdlet que="" desejo="" obter="" maiores="" inform="">命令查询帮助。</cmdlet>

<figure>[![](img/be3ff3ac1d7afd212dfb16b59f3322a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xR8qUkK3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3cX56Hpm7PLzNcxLMIkvkg.png) 

<figcaption>Retorno do 帮助 para o cmdlet 安装-模块。</figcaption>

</figure>

我们可以使用 **switchparameter -Detailed** 返回详细的安装模块信息。

[![](img/084efdb74f82c0ec4840e765ecf31139.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--woU_sldl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgigE-mvSfts82Ft62ymgtQ.png)

<figure>[![](img/a3a6518a6eb7c00e6986e277b0329ff1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bSZscLnF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AK_EvdxWpjSPF0u1Qdmgo9w.png)

<figcaption>Retorno do Get-Help-detailed</figcaption>

</figure>

我们可以搜索 cmdlet，并使用参数指定要更好地了解的参数。

<figure>[![](img/ec501c10c4bf2465968c5c10ba59efed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OxKdFus5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/917/1%2Af_Wl6uCu2ehpRZe7daufnA.png)

<figcaption>Retorno do Get-Help<cmdlet>-Parameter<Parameter o></figcaption>

</figure>

在下面的示例中，我们可以返回使用‘t0’switch parameter-examples’来使用 cmdlet 的示例。

<figure>[![](img/5d46ef5a0468c62f6445bf59ca15d808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bfwSGn2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALmraPnSDkFZLyaEJOuTYEw.png) 

<figcaption>获取-帮助获取-流程-实例</figcaption>

</figure>

最后，我们可以请求在线帮助页面。

<figure>[![](img/9f8d453bb94946d0201ed117ae494916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DLg-agI1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmJ6At6ObM8L92iWbHUuM3Q.png) 

<figcaption>获取-帮助获取-流程-在线</figcaption>

</figure>

默认情况下，PowerShell 附带的帮助信息最少，以支持存储空间有限的设备(如 Raspberry)，更新帮助会将信息保存到 Shell 中，您可能需要提升权限才能下载完整的帮助。

好了，各位。如有疑问:获得帮助

别忘了， **PS 是生命**