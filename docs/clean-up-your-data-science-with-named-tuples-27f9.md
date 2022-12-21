# 用命名元组清理您的数据科学

> 原文：<https://dev.to/waylonwalker/clean-up-your-data-science-with-named-tuples-27f9>

这篇文章来自 waylonwalker.com 的档案，在我发帖给 DEV 之前。

* * *

如果你是 TalkPython 或 PythonBytes 的忠实听众，你会多次听到 Michael Kennedy 谈论命名元组，但它们是什么以及它们如何融入我的数据科学工作流。

## 举例

当你把你的脚本分成模块和库的时候，你可能开始注意到你需要把大量的数据传递给你创建的所有函数。例如，如果您正在利用`sales`、`inventory`和`pricing`数据运行一些分析。您可能需要计算总收入和现有库存。您可能需要将这些数据集传递到各种模型中，以根据预测的数量来推动生产或定价。

## 加载数据

这里我们设置了可以从销售数据库加载数据的函数。假设我们也有类似于`get_inventory`和`get_pricing`的功能。

```
def get_engine():
    engine = create_engine('postgresql://scott:tiger@localhost:5432/mydatabase')

def get_sales():
    '''
    gets sales history from the sales database
    '''
    engine = get_engine()
    with engine.connect() as con:
        sql = '''select * from sales.history'''
        df = pd.read_sql(sql, con)
    engine.dispose()
    return df

def get_inventory():
    ...

def get_pricing():
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 创建指标

这里我们创建第一个函数来计算一些指标。很可能有许多这样的函数重复类似的模式。它们使用相似的数据，并有自己的自定义计算和连接逻辑。

```
def calculate_total_revenue(sales, pricing):
    """calculates the total sales revenue for all of company XYZ"""
    sales = sales.join(pricing.set_index('sku'), on='sku')
    sales['revenue'] = sales['qty'] * sales['price']
    return sales['revenue'].sum()

def calculate_inventory_sale_ratio(sales, inventory, pricing):
    ...

def calculate_inventory_sale_ratio(inventory, sales):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

此外，需要在某个地方调用这些函数，这可能是在一个`make_report()`函数中，该函数将这些数据放入一个 html 模板中发送给利益相关者，或者在一个网站上提供。请注意我们是如何一次又一次地显示相同的数据的。有时我们甚至会以不同的顺序要求它😲。在项目的早期，在事情失控之前认识到这一点是很重要的。

```
def make_report():
    """Makes stakeholder report for company XYZ"""
    sales = get_sales()
    inventory = get_inventory()
    pricing = get_pricing()

    revenue = calculate_total_revenue(sales, pricing)
    sales_ratio = inventory_sale_ratio(sales, inventory, pricing)
    inventory_sale_ratio(inventory, sales) 
```

Enter fullscreen mode Exit fullscreen mode

### 失控

在这个过程中，我们的特性、模型和外部主管都有自己的需求，我们添加了新的数据集和几个标志。这是焦虑开始蔓延的时候。我们开始花大量时间反复检查每个调用的顺序，以确保不会出错。当其他人触摸这个模型时，我们知道它看起来像什么，不禁想，“哦，上帝，我希望他们没有搞砸那个可怕的模块！”

```
def calculate_total_revenue(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    sales = (sales
                .join(pricing.set_index('sku'), on='sku')
                .join(inventory.set_index('sku'), on='sku')
            )
    sales['revenue'] = sales['qty'] * sales['price']
    sales = sales.query(f'price > {min_price}')
    sales = sales.query(f'sale_date > {min_date}')
    sales = sales.query(f'sale_date < {end_date}')
    return sales['revenue'].sum()

def calculate_inventory_sale_ratio(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    ...

def calculate_inventory_sale_ratio(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    ...

def model_new_prices(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    ...

def model_production(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    ...

def completely_custom_metric_for_steve(sales, pricing, inventory, stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000'):
    """Steve has been here 30 years and doesnt trust our metrics unless he can validate against the old metrics"""
    ... 
```

Enter fullscreen mode Exit fullscreen mode

当您开始调用所有这些函数时，情况会变得更加疯狂！请注意，我们有一个相同数据传递到
的共同主题

```
def make_report(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000', is_for_steve=False):
    """Makes stakeholder report for company XYZ"""
    sales = get_sales()
    inventory = get_inventory()
    pricing = get_pricing()

    revenue = calculate_total_revenue(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')
    sales_ratio = sales_ratio = inventory_sale_ratio(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')
    inventory_sale_ratio = inventory_sale_ratio(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')
    predicted_prices = model_new_prices(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')
    predicted_production_rates = model_production_rate(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')
    completely_custom_metric_for_steve(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000')

    # render report
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 这段代码臭死了

⌚ *重构的时间*

虽然我们的代码开始时很好，但随着时间的推移，它变得很糟糕。它会工作得很好，但它可以更好，更愉快地工作。我们这里有一种叫做`data clump`的代码气味。这是一组总是一起出现的数据。将这些数据正式组合在一起是合适的。在 python 中做到这一点的一种方法是使用名称元组。这是一个非常简单的技术，它允许我们定义一个包含一组特定属性的数据结构，我们可以使用`.attribute_name`来访问这些属性。用一组正式的数据替换这个`data clump`将允许我们减少所有函数中的参数数量。轻松添加新属性。而不依赖于立场论点。这段代码将更容易维护、阅读和编写。

> 我们这里有一种叫做`data clump`的代码气味

**创建一个命名的元组**

```
from collections import namedtuple

ModelData = namedtuple('modin_data', 'sales pricing inventory stored_at min_price start_date end_date') 
```

Enter fullscreen mode Exit fullscreen mode

**使用命名的元组**

```
data = ModelData(
    sales=get_sales(),
    pricing=get_pricing(),
    inventory=get_inventory(),
    stored_at='LAX'
    min_price=100
    start_date = datetime.today() - datetime.timedelta(days=30)
    end_date = datetime.today()
    ) 
```

Enter fullscreen mode Exit fullscreen mode

**重构函数**现在我们有了一个干净的数据对象，我们该如何使用它呢？很简单，我们传入一个数据对象，然后用点操作符访问每个属性。这些函数现在更容易调用和读取。在这里，我为我们的`data`选择了一个糟糕的名字，但是在真实的场景中，你可能有多个`namedtuples`。

```
def calculate_total_revenue(data):
    sales = (data.sales
                .join(data.pricing.set_index('sku'), on='sku')
                .join(data.inventory.set_index('sku'), on='sku')
            )
    sales['revenue'] = sales['qty'] * sales['price']
    sales = sales.query(f'price > {data.min_price}')
    sales = sales.query(f'sale_date > {data.min_date}')
    sales = sales.query(f'sale_date < {data.end_date}')
    return sales['revenue'].sum()

... 
```

Enter fullscreen mode Exit fullscreen mode

**调用函数**

既然所有的数据都存储在一个对象中，那么使用一个数据实例来调用我们的每个函数就非常容易了。

```
def make_report(stored_at='LAX', min_price=100, start_date='01-01-1999', end_date='01-01-3000', is_for_steve=False):
    """Makes stakeholder report for company XYZ"""
    data = ModelData(stored_at=stored_at, min_price=min_price, start_date=start_date, end_date=end_date)

    revenue = calculate_total_revenue(data)
    sales_ratio = sales_ratio = inventory_sale_ratio(data)
    inventory_sale_ratio = inventory_sale_ratio(data)
    predicted_prices = model_new_prices(data)
    predicted_production_rates = model_production_rate(data)
    completely_custom_metric_for_steve(data)

    # render report
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 🧹清理你的科学数据

命名元组是清理数据科学代码和减少`Data Clumps`的一个很好的方法。任何时候你有多个几乎总是一起使用的数据集`namedtuple`都是清理你的代码的好方法，使它更具可读性和可维护性。如果您发现您也有与该数据紧密耦合的函数，您可能想考虑使用`class`而不是`namedtuple`，但这是另一篇文章的内容。

* * *

| 我一直在我的[时事通讯](https://waylonwalker.com/newsletter)中写一些关于我进入科技/数据行业的想法的小片段，👉看看吧，让我们开始对话。 | [![Sign up for my Newsletter](img/27ef239b050e43e8f034dd5c15c6e7e6.png)](https://waylonwalker.com/newsletter) |
|  |  | [![](img/dbdc689655a3cf3cc1c010ba4549003c.png)](https://twitter.com/_waylonwalker) | [![](img/e08fb48b180381aab923a56009c6540c.png)](https://instagram.com/_waylonwalker) | [![](img/42c8bb55d0d2b1a838a944d8824f8fb0.png)](https://www.linkedin.com/in/waylonwalker/) | [![](img/980e79f075b926fc027f0bd551594d03.png)](https://www.buymeacoffee.com/bBdtMQO) |
|  | 👀看到一个问题，在 [GitHub](https://github.com/WaylonWalker/waylonwalkerv2/edit/main/src/pages/blog/named-tuples-data-science.md) 上编辑这个帖子 |