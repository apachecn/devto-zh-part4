# 在 Fluence 网络上部署 WASM 文件

> 原文：<https://dev.to/lcoenen/deploying-a-wasm-file-on-fluence-network-23e9>

嘿伙计们！

今天，为了得到[金币赏金](https://gitcoin.co/issue/fluencelabs/Bounties/1/3290)，我将把我们自己的 [Koos 后端](https://gitlab.com/koos-project/koos/tree/master/server)部署到[流量网络](//fluence.network)。

[![Fluence logo](img/79eb45f3305bacc9b0e6036ec41faab6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Zzu5rkH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gaShYMo.png)

Fluence 是一个基于以太坊的去中心化云平台，激励矿工运行 [webassembly code](http://webassembly.org) 。更好的是，他们将 SQLite 和 Redis 编译成 SQL，并提供 Rust、C 和 [Assemblyscript](https://docs.assemblyscript.org/) 中的 SDK。鉴于我们在打字方面的熟练程度，我们决定采用后者。

不要说谎，至少经历坎坷。JSON 库需要更新，我怀疑一些`.trim()`调用只是在我的字符串末尾吸取了一些字符，等等。另一方面，向 Fluence 团队大声喊出来，因为他们真的非常积极和支持。你们所做的真的很有创意，继续努力吧！< 3 < 3

因此，结果是他们直接在[仪表板](//dash.fluence.network)中实现了多模块。我所要做的就是连接 Metamask 并上传仪表板下的两个 WASM 文件。

[![Fluence dashboard](img/4a68b441b6797c0ab62c47a47924d535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eyQ4XOic--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xdmxk0J.png)

然后，过了一会儿，就这样，我的应用程序被部署了，我可以访问`AppId`和合同地址了！我所要做的就是在我的前端配置中复制粘贴这些值，并调用 fluence-js 库，如下所示:

```
 return fluence
      .connect(
        config.FLUENCE_CONTRACT_ADDRESS,
        config.FLUENCE_APP_ID,
        config.FLUENCE_ETHEREUM_URL
      )

/// ...

    const request = this.fluence.request({my request})

    return request
      .result()
      .then(answer => answer.asString())
      .then(answer => JSON.parse(answer))
      .then(answer => {
      // Do something with the answer 
```