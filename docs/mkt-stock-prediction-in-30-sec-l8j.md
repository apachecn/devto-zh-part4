# 30 秒内的股票预测

> 原文：<https://dev.to/loaiabdalslam/mkt-stock-prediction-in-30-sec-l8j>

# [MKT](https://github.com/loaiabdalslam/MKT)

js 是一个交换价格服务，股票，加密货币，股票预测和更多\
这个软件包包含数百种货币，加密货币和股票价格。\
6，096 枚硬币，283，037 个交易对，31 个新闻提供商它还与 TensorFlow 合作在此阅读更多信息[阅读更多关于加密比较服务的信息](https://min-api.cryptocompare.com/faq)
使用 RNN 进行市场预测/股票预测，还使用 canvas.js 可视化股票数据

## 依赖关系

*   神经网络(brain.js)
*   Tensorflow Framework (tensorflow.js)
*   数据可视化(canvas.js)
*   主要 Api(min-api.cryptocompare.com)

### 入门:

#### 1 -获得完整的详细响应(多示例 fsym & tsym)

```
const { MKT } = require('@mkt-eg/mkt')

const mkt = new MKT(
  'bbbc22c3a13c74456a6d4bb7ba5745476ebfdc81c867fc240258122b78eb6a6f'
)
const data = mkt
  .exchange({
    fsym: 'BTC',
    tsyms: 'USD',
    type: 'full'
  })
  .then(response => {
    console.log(JSON.stringify(response.data))
  })
  .catch(error => {
    console.log(error)
  })

// JSON OUTPUT 
/* 
{
   "RAW":{
      "BTC":{
         "USD":{
            "TYPE":"5",
            "MARKET":"CCCAGG",
            "FROMSYMBOL":"BTC",
            "TOSYMBOL":"USD",
            "FLAGS":"2",
            "PRICE":9885.11,
            "LASTUPDATE":1563398729,
            "LASTVOLUME":0.1,
            "LASTVOLUMETO":986.6100000000001,
            "LASTTRADEID":"379345663",
            "VOLUMEDAY":93692.97987050914,
            "VOLUMEDAYTO":893517565.3549776,
            "VOLUME24HOUR":104598.9946433591,
            "VOLUME24HOURTO":997000834.8997525,
            "OPENDAY":9423.44,
            "HIGHDAY":9982.24,
            "LOWDAY":9086.51,
            "OPEN24HOUR":9649.99,
            "HIGH24HOUR":9988.35,
            "LOW24HOUR":9076.48,
            "LASTMARKET":"Bitfinex",
            "VOLUMEHOUR":2210.51459713301,
            "VOLUMEHOURTO":21755061.31969251,
            "OPENHOUR":9692.2,
            "HIGHHOUR":9943.53,
            "LOWHOUR":9663.39,
            "TOPTIERVOLUME24HOUR":101424.52271706509,
            "TOPTIERVOLUME24HOURTO":966363837.9391046,
            "CHANGE24HOUR":235.1200000000008,
            "CHANGEPCT24HOUR":2.436479208786753,
            "CHANGEDAY":461.6700000000001,
            "CHANGEPCTDAY":4.899166334162472,
            "SUPPLY":17823212,
            "MKTCAP":176184411173.32,
            "TOTALVOLUME24H":720083.9899007804,
            "TOTALVOLUME24HTO":7081137716.36884,
            "TOTALTOPTIERVOLUME24H":425384.18596477184,
            "TOTALTOPTIERVOLUME24HTO":4168740744.7056427,
            "IMAGEURL":"/media/19633/btc.png"
         }
      }
   },
   "DISPLAY":{
      "BTC":{
         "USD":{
            "FROMSYMBOL":"Ƀ",
            "TOSYMBOL":"$",
            "MARKET":"CryptoCompare Index",
            "PRICE":"$ 9,885.11",
            "LASTUPDATE":"Just now",
            "LASTVOLUME":"Ƀ 0.1000",
            "LASTVOLUMETO":"$ 986.61",
            "LASTTRADEID":"379345663",
            "VOLUMEDAY":"Ƀ 93,693.0",
            "VOLUMEDAYTO":"$ 893,517,565.4",
            "VOLUME24HOUR":"Ƀ 104,599.0",
            "VOLUME24HOURTO":"$ 997,000,834.9",
            "OPENDAY":"$ 9,423.44",
            "HIGHDAY":"$ 9,982.24",
            "LOWDAY":"$ 9,086.51",
            "OPEN24HOUR":"$ 9,649.99",
            "HIGH24HOUR":"$ 9,988.35",
            "LOW24HOUR":"$ 9,076.48",
            "LASTMARKET":"Bitfinex",
            "VOLUMEHOUR":"Ƀ 2,210.51",
            "VOLUMEHOURTO":"$ 21,755,061.3",
            "OPENHOUR":"$ 9,692.20",
            "HIGHHOUR":"$ 9,943.53",
            "LOWHOUR":"$ 9,663.39",
            "TOPTIERVOLUME24HOUR":"Ƀ 101,424.5",
            "TOPTIERVOLUME24HOURTO":"$ 966,363,837.9",
            "CHANGE24HOUR":"$ 235.12",
            "CHANGEPCT24HOUR":"2.44",
            "CHANGEDAY":"$ 461.67",
            "CHANGEPCTDAY":"4.90",
            "SUPPLY":"Ƀ 17,823,212.0",
            "MKTCAP":"$ 176.18 B",
            "TOTALVOLUME24H":"Ƀ 720.08 K",
            "TOTALVOLUME24HTO":"$ 7.08 B",
            "TOTALTOPTIERVOLUME24H":"Ƀ 425.38 K",
            "TOTALTOPTIERVOLUME24HTO":"$ 4.17 B",
            "IMAGEURL":"/media/19633/btc.png"
         }
      }
   }
}

*/ 
```

Enter fullscreen mode Exit fullscreen mode

#### 2 -获取单一价格响应(仅限单一 Ftsym)

```
const { MKT } = require('@mkt-eg/mkt')

const mkt = new MKT(
  'bbbc22c3a13c74456a6d4bb7ba5745476ebfdc81c867fc240258122b78eb6a6f'
)
const data = mkt
  .exchange({
    fsym: 'BTC', // Single Fysm only 
    tsyms: 'USD,EGP', // Multiaple Tsyms is allowed
    type: 'single'
  })
  .then(response => {
    console.log(JSON.stringify(response.data))
  })
  .catch(error => {
    console.log(error)
  })

// JSON OUTPUT 

{
   "USD":9888.01,
   "EGP":182256.26
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 3 -获得多重价格响应

```
const { MKT } = require('@mkt-eg/mkt')

const mkt = new MKT(
  'bbbc22c3a13c74456a6d4bb7ba5745476ebfdc81c867fc240258122b78eb6a6f'
)
const data = mkt
  .exchange({
    fsym: 'BTC,ETH', // Single Fysm only 
    tsyms: 'USD,EGP', // Multiaple Tsyms is allowed
    type: 'multi'
  })
  .then(response => {
    console.log(JSON.stringify(response.data))
  })
  .catch(error => {
    console.log(error)
  })

// JSON OUTPUT 

{
   "BTC":{
      "USD":9906.65,
      "EGP":182256.26
   },
   "ETH":{
      "USD":215.27,
      "EGP":3964.07
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我提出的一些想法，你可以开始:

*   添加自然语言处理，以增加对已预测价格的信心
*   增加投资过程的模拟和一些交易策略的发展。
*   监控市场并制作一个全球仪表盘。
*   使用 MKT 添加简单和示例。射流研究…

## 贡献

*   对于第一个贡献者，你可以删除文件，成为第一个股东(我留给你)
*   至于其他方面，如果你想到了一个主意，你应该立即提出请求并付诸实施。

作者:Loaii abdalslam