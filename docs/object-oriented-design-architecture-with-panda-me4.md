# 熊猫的面向对象设计架构

> 原文：<https://dev.to/fpim/object-oriented-design-architecture-with-panda-me4>

嗨伙计们。今天我想谈谈如何实现面向对象(OO)设计，用 Python 和 Pandas 构建一个商业智能模块。

Pandas 被广泛用于数据分析，它很容易使用，人们可以很容易地使用 pandas 建立一个数百行代码的脚本来处理复杂的数据。然而，在与许多数据分析师合作后，我发现数据分析师制作的大多数脚本应该属于“过程代码”的类别，这是不可重用的。稍微改变一下上下文通常会导致完全重写代码。如果您处于“研究”领域，您的代码被用于“探索”或“实验”目的，这是没问题的，但是如果您要编写有助于更大系统的代码或要重复运行的代码，您需要实现一些架构。

## 我的背景

我在财务部门工作，每天都需要处理复杂关系的数据。我收到的原始数据通常太“原始”，以至于从中提取任何有用的信息通常需要很多步骤。由于原始数据结构和业务逻辑不匹配，代码很容易变得无法维护。

#### 解

很快我意识到我需要在原始数据之上的一个面向对象层来处理所有的复杂性。我在这里使用术语 **OO 层**的原因是 OO 本质上是一个抽象层。**它让你只关注业务逻辑，而不必担心如何从原始数据实现业务。**当你试图编辑代码库时，架构就是要减轻你的认知负担。

# 数据

我们将尝试为一些贸易数据建立一个面向对象模型:

| 账户， | 股票 _ 代码， | BuySell, | 日期， | 数量， | 单价 |
| --- | --- | --- | --- | --- | --- |
| One thousand and one | 001 | B | Twenty million one hundred and ninety thousand one hundred and five | eight | Two |
| One thousand and one | 002 | B | Twenty million one hundred and ninety thousand one hundred and five | four | three |
| One thousand and one | 002 | S | Twenty million one hundred and ninety thousand one hundred and six | three | one |
| One thousand and one | 003 | B | Twenty million one hundred and ninety thousand one hundred and six | six | five |
| One thousand and one | 003 | S | Twenty million one hundred and ninety thousand one hundred and six | four | six |
| One thousand and one | 001 | S | Twenty million one hundred and ninety thousand one hundred and seven | six | three |
| One thousand and two | 001 | B | Twenty million one hundred and ninety thousand one hundred and five | six | Two |
| One thousand and two | 002 | B | Twenty million one hundred and ninety thousand one hundred and five | eight | three |
| One thousand and two | 002 | S | Twenty million one hundred and ninety thousand one hundred and six | three | one |
| One thousand and two | 003 | B | Twenty million one hundred and ninety thousand one hundred and six | five | five |
| One thousand and two | 003 | S | Twenty million one hundred and ninety thousand one hundred and six | four | six |
| One thousand and two | 001 | S | Twenty million one hundred and ninety thousand one hundred and seven | six | three |

一些账户信息:

| 账户， | 名字 |
| --- | --- |
| One thousand and one | 大卫 |
| One thousand and two | 汤姆(男子名) |

还有一些收盘价数据:

| 股票 _ 代码， | 收盘价格， | 数据 |
| --- | --- | --- |
| 001 | Two | Twenty million one hundred and ninety thousand one hundred and five |
| 001 | three | Twenty million one hundred and ninety thousand one hundred and six |
| 001 | Two | Twenty million one hundred and ninety thousand one hundred and seven |
| 002 | Two | Twenty million one hundred and ninety thousand one hundred and five |
| 002 | three | Twenty million one hundred and ninety thousand one hundred and six |
| 002 | five | Twenty million one hundred and ninety thousand one hundred and seven |
| 003 | five | Twenty million one hundred and ninety thousand one hundred and five |
| 003 | six | Twenty million one hundred and ninety thousand one hundred and six |
| 003 | seven | Twenty million one hundred and ninety thousand one hundred and seven |

首先，我们将有一个“数据层”来处理源数据的 i/o。由于我们没有实际的数据源，我们将
模拟它:

```
import pandas as pd
from io import StringIO

class Data():

    def trade_df(self):
        # This method should handle the import of source data
        data = StringIO()

        s = """
        Account|Stock_Code|BuySell|Date|Quantity|Unit_Price
        1001|001|B|20190105|8|2
        1001|002|B|20190105|4|3
        1001|002|S|20190106|3|1
        1001|003|B|20190106|6|5
        1001|003|S|20190106|4|6
        1001|001|S|20190107|6|3
        1002|001|B|20190105|6|2
        1002|002|B|20190105|8|3
        1002|002|S|20190106|3|1
        1002|003|B|20190106|5|5
        1002|003|S|20190106|4|6
        1002|001|S|20190107|6|3
        """

        data.write(s.replace(' ',''))
        data.seek(0)
        df = pd.read_csv(data,sep='|',dtype={'Account':str,
                                                'Date':str,
                                                'Stock_Code':str,
                                                "Date":str})
        df.Date = pd.to_datetime(df.Date,format='%Y%m%d')
        return df

    def account_df(self):
        data = StringIO()

        s = """
        Account|Name
        1001|David
        1002|Tom
        """

        data.write(s.replace(' ',''))
        data.seek(0)
        df = pd.read_csv(data, sep='|', dtype={'Account': str,
                                                  'Name': str})
        return df

    def stock_prices(self):

        data = StringIO()

        s = """
        Stock_Code|Closing_Price|Date
        001|2|20190105
        001|3|20190106
        001|2|20190107
        002|2|20190105
        002|3|20190106
        002|5|20190107
        003|5|20190105
        003|6|20190106
        003|7|20190107
        """

        data.write(s.replace(' ',''))
        data.seek(0)
        df = pd.read_csv(data, sep='|', dtype={'Stock_Code': str,
                                                  'Date': str})
        df.Date = pd.to_datetime(df.Date,format='%Y%m%d')
        return df 
```

`Data`对象应该只处理数据的导入和验证，而不应该实现业务逻辑。

然后在`Data`对象之上，我们将构建我们的第一个面向对象层:

```
class Book():
    def __init__(self):
        self._data = Data()

    @property
    def trade_book_df(self):
        if not hasattr(self,'_trade_book_df'):
            df = self._data.trade_df().join(self._data.account_df().set_index('Account'),on='Account')
            df['Trade_Amount'] = df.Quantity * df.Unit_Price
            self._trade_book_df = df
        return self._trade_book_df

    def stock_prices(self,date=None):
        df = self._data.stock_prices()
        if date:
            df = df[df.Date == date]
        return df

    @property
    def holdings(self):
        return Holdings(self)

    @property
    def accounts(self):
        return Accounts(self.holdings)

Book().trade_book_df

|    |   Account |   Stock_Code | BuySell   | Date                |   Quantity |   Unit_Price | Name   |   Trade_Amount |
|---:|----------:|-------------:|:----------|:--------------------|-----------:|-------------:|:-------|---------------:|
|  0 |      1001 |          001 | B         | 2019-01-05 00:00:00 |          8 |            2 | David  |             16 |
|  1 |      1001 |          002 | B         | 2019-01-05 00:00:00 |          4 |            3 | David  |             12 |
|  2 |      1001 |          002 | S         | 2019-01-06 00:00:00 |          3 |            1 | David  |              3 |
|  3 |      1001 |          003 | B         | 2019-01-06 00:00:00 |          6 |            5 | David  |             30 |
|  4 |      1001 |          003 | S         | 2019-01-06 00:00:00 |          4 |            6 | David  |             24 |
|  5 |      1001 |          001 | S         | 2019-01-07 00:00:00 |          6 |            3 | David  |             18 |
|  6 |      1002 |          001 | B         | 2019-01-05 00:00:00 |          6 |            2 | Tom    |             12 |
|  7 |      1002 |          002 | B         | 2019-01-05 00:00:00 |          8 |            3 | Tom    |             24 |
|  8 |      1002 |          002 | S         | 2019-01-06 00:00:00 |          3 |            1 | Tom    |              3 |
|  9 |      1002 |          003 | B         | 2019-01-06 00:00:00 |          5 |            5 | Tom    |             25 |
| 10 |      1002 |          003 | S         | 2019-01-06 00:00:00 |          4 |            6 | Tom    |             24 |
| 11 |      1002 |          001 | S         | 2019-01-07 00:00:00 |          6 |            3 | Tom    |             18 | 
```

1.  `Book`对象有一个拥有`Data`对象的`_data`属性。
2.  `trade_book_df`属性实现了两个业务逻辑:
    *   `trade_df`和`account_df`的连接。
    *   `Trade_Amount`->-`Quantity`*`Unit_Price`的定义。
3.  `stock_prices`方法提取特定日期的股票价格。
4.  它提供了我们将要讨论的`Holdings`和`Accounts`上下文的入口。

## 语境

" Context "是你想要查看数据的维度，所有具有相同维度的视图应该封装在一个对象中
。

例如，要查看“持有”上下文中的数据，我们需要:

1.  汇总到某个时间点的交易数据。
2.  将持股数量与股票收盘价相乘得到市值。

```
class Holdings():
    def __init__(self,book):
        self._book = book

    def holdings_of(self,date):
        trades_df = self._book.trade_book_df
        date_hld_df = trades_df[trades_df.Date <= date]
        date_hld_df['qnt_change'] = date_hld_df['BuySell'].map({'B':1,'S':-1}) * date_hld_df.Quantity
        hld_df = date_hld_df.groupby(['Account','Stock_Code'],as_index=False)\
            .agg({'qnt_change':sum})\
            .rename(columns={'qnt_change':'Holdings'})
        hld_df = hld_df.join(self._book.stock_prices(date).set_index('Stock_Code'),on='Stock_Code')
        hld_df['Market_Value'] = hld_df.Closing_Price * hld_df.Holdings
        return hld_df

from datetime import date
Book().holdings.holdings_of(date(2019,1,6))

|    |   Account |   Stock_Code |   Holdings |   Closing_Price | Date                |   Market_Value |
|---:|----------:|-------------:|-----------:|----------------:|:--------------------|---------------:|
|  0 |      1001 |          001 |          8 |               3 | 2019-01-06 00:00:00 |             24 |
|  1 |      1001 |          002 |          1 |               3 | 2019-01-06 00:00:00 |              3 |
|  2 |      1001 |          003 |          2 |               6 | 2019-01-06 00:00:00 |             12 |
|  3 |      1002 |          001 |          6 |               3 | 2019-01-06 00:00:00 |             18 |
|  4 |      1002 |          002 |          5 |               3 | 2019-01-06 00:00:00 |             15 |
|  5 |      1002 |          003 |          1 |               6 | 2019-01-06 00:00:00 |              6 | 
```

然后，您可以在上下文的基础上构建上下文。例如，您可以在
`Holdings`上下文:
之上构建一个`Accounts`上下文

```
class Accounts():
    def __init__(self,holdings):
        self._holdings = holdings

    def account_value(self,date):
        df = self._holdings.holdings_of(date)
        return df.groupby('Account',as_index=False).agg({'Market_Value':sum,
                                                         'Date':'first'})

Book().accounts.account_value(date(2019,1,6))

|    |   Account |   Market_Value | Date                |
|---:|----------:|---------------:|:--------------------|
|  0 |      1001 |             39 | 2019-01-06 00:00:00 |
|  1 |      1002 |             39 | 2019-01-06 00:00:00 | 
```

# 总结

以上是一个简单的例子，说明了如何将面向对象设计应用到熊猫脚本中。该体系结构允许您随着数据源数量的增加而轻松扩展。这只是一个非常简单的介绍，还有很多建模技术可以用来管理复杂的数据。最后，我想强调的是，这些技术变得越来越重要。
传统上，这种问题可以由数据库和关系模型来处理，但是考虑到数据日益增长的复杂性和分散性，您更有可能遇到数据库无法处理所有事情的情况，您将需要代码的一些补充，
尤其是当您需要来自多个数据源的数据时。