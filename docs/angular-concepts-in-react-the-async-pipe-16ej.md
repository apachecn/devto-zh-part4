# React 中的角度概念:异步管道

> 原文：<https://dev.to/lysofdev/angular-concepts-in-react-the-async-pipe-16ej>

Angular 框架中我最喜欢的工具之一是异步管道。它允许我将一个可观察对象的最新值呈现到屏幕上。每次收到新的通知时，组件将重新呈现以显示可观察对象的下一个值。最后，一旦组件拆除，对可观察对象的订阅将被处理掉。自动！它允许我在整洁的 RxJs 流中构建我的异步代码。

一个常见的用途是从 NgRx 这样的数据存储中同步一些值，并通过一些调整(如格式化)将其显示在组件中。我不需要使用 OnInit 生命周期方法来开始订阅，也不需要使用 OnDestroy 生命周期方法来取消订阅。

现在，我在这里不是要告诉你，你应该将 RxJs 与 React 一起使用。我知道没有它我当然可以构建一个更简单的应用程序，但是如果我发现自己在与许多 API 一起工作，并且有大量的承诺要完成，那么我认为 RxJs 是值得的。出于这个原因，我想与您分享我个人将 Angular 的异步管道作为 React 组件的实现。

# 一个简单的例题——股票价格

我们将构建一个理论上的股票监控工具，该工具接收股票价格信息，我们可以通过每只股票的报价器对其进行过滤。我们需要一个组件来显示股票的报价和最新价格。假设我们有一个服务，它将这个值作为一个可观察的数字值流提供，我们需要将它重新格式化为一个带有美元符号和两点精度的字符串。很简单，对吧？

# 角度 2+中的异步管道

我们的 Angular 组件将使用服务中的 API，格式化最新的值，并将它们打印到屏幕上。

```
import { Component, Input } from '@angular/core';
import { StockPricesService } from './stock-prices.service';
import { map } from 'rxjs/operators';

@Component({
  selector: 'app-stock-price',
  templateUrl: 'stock-price.component.html'
})
export class StockPriceComponent {
  @Input() ticker: string;

  price$ = this.stockPrices.getStockPrice(this.ticker).pipe(
    map((price: number) => `$${price.toFixed(2)}`)
  );

  constructor(private stockPrices: StockPricesService) {}
} 
```

我们组件的 HTML 看起来会像这样:

```
<article>
  <section>{{ ticker }}</section>
  <section>{{ price$ | async }}</section>
</article> 
```

最后，我们可以这样使用这个组件:

```
<app-stock-price [ticker]="'GE'"></app-stock-price> 
```

# 股价组件的 React 版本

下面是我们如何在 React 中实现同样的组件:

```
import React, { useMemo } from "react";
import { map } from "rxjs/operators";

import { Async } from "./async";
import { useStockPrices } from "./use-stock-prices";

export interface StockPriceProps {
  ticker: string;
}

export function StockPrice({ ticker }: StockPriceProps): JSX.Element {
  const { getStockPrice } = useStockPrices();
  const price$ = useMemo(
    () => getStockPrice(ticker).pipe(map((price: number) => `$${price.toFixed(2)}`)),
    [ticker]
  );
  return (
    <article>
      <section>{ticker}</section>
      <section>
        <Async>{price$}</Async>
      </section>
    </article>
  );
} 
```

显然，`Async`组件还不存在，所以我们必须实现它以获得想要的效果。我是这样做的:

```
import React, { PropsWithChildren, useState, useEffect } from "react";
import { Observable, isObservable, Subscription } from "rxjs";

type AsyncProps<T> = PropsWithChildren<{ observable?: Observable<T> }>;

export function Async<T = any>({
  observable,
  children
}: AsyncProps<T>): JSX.Element {
  const [value, setValue] = useState<T | null>(null);
  useEffect(() => {
    let subscription: Subscription;
    if (isObservable(observable)) {
      subscription = observable.subscribe(setValue);
    } else if (isObservable(children)) {
      subscription = (children as Observable<T>).subscribe(setValue);
    }
    return () => subscription.unsubscribe();
  }, [observable, children]);
  return <>{value}</>; } 
```

请注意，我们也可以像这样将可观察对象作为道具传递:

```
<Async observable={price$} /> 
```

# 这甚至有用吗？

如果您像我一样是一个持怀疑态度的开发人员，您可能已经注意到，这几乎没有为我们节省任何麻烦，因为我们只是对股票价格组件进行了有意的订阅。我们仍然需要使用`useMemo`钩子在可观察对象上应用管道转换。这完全取决于你想让你的组件同步到多少个可观察的。如果你只有一个，那么它可能不值得，但是如果你有一个或两个以上，或者如果你只是想直接显示可观察的东西而不进行变换，那么它可能非常值得。

你怎么想呢?