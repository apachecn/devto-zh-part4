# 打字稿。如何给类方法添加属性？

> 原文：<https://dev.to/borovik96/typescript-how-to-add-a-property-to-class-method-1cbg>

我有下一个代码:

```
 export const getPath = async (from: CurrencyTicker, to: CurrencyTicker): Promise<ExchangePair[]> => {
  const helper = new Helper(from, to);
  const path = await helper.calculatePath()

  return path;
}
getExchangePath.cached = withMemoryCache('exchange-path', CACHE_PAIR_COUNT, ms('5m'), getExchangePath); 
```

它工作得很好，但是我怎么用类做同样的事情呢？

类似这样的事情

```
class Estimate {
  private readonly helper: Helper;

  constructor(currencyFrom: CurrencyTicker, currencyTo: CurrencyTicker) {
    this.helper = new Helper(currencyFrom, currencyTo);

    // next line throws TS error Property 'cached' does not exist on type '() => Promise<[string, string][]>'
    this.getExchangePath.cached = withMemoryCache('exchange-path', CACHE_PAIR_COUNT, ms('5m'), getExchangePath);
  }

  getPath = async (): Promise<ExchangePair[]> => {
    const path = await this.helper.calculatePath()

    return path;
  }
} 
```