# Python3 和 Pandas 的犯罪数据探索:第 2 部分

> 原文：<https://dev.to/_rich/crime-data-exploration-with-python3-and-pandas-part-2-23l1>

## 目标

本系列文章的目的是探索来自 UCR (Uniforn 犯罪报告项目)的数据集。在本帖中，我们将使用熊猫来深入挖掘凶杀案的犯罪统计数据。准备好用于比较的数据集后，我们将根据人均犯罪数量对每个州进行排名。

跟随或检查 Github 上的代码。

## 关于数据

我们正在使用的数据集是从 [Cime 数据浏览器](https://crime-data-explorer.fr.cloud.gov/)网站下载的`estimated_crimes.csv`。该文件包含美国从 1995 年到 2017 年的 7 种犯罪类型的估计犯罪。有关该数据集及其编译方式的更多信息，请访问 [Cime 数据浏览器。](https://crime-data-explorer.fr.cloud.gov/)

## 准备数据

对于数据操作，我们将使用熊猫。我们需要做的第一件事是下载数据集，并将其加载到熊猫数据框架中。

如果你需要数据集，你可以在这里下载:[https://github.com/rbk/Crime-Data-Analysis](https://github.com/rbk/Crime-Data-Analysis)

在下面的代码中，我们使用 pandas 打开 **estimated_crimes.csv** 到 [dataframe](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) 。

我们通过指定 **usecols** 属性来指定想要处理的列。这个属性告诉 **read_csv** 函数只加载带有列数组中标题的列:

```
import pandas

cols = [
    'year',
    'state_name',
    'population',
    'homicide',
]

raw_data = pandas.read_csv('../data/estimated_crimes.csv', usecols=cols)

print(raw_data.head()) 
```

### 输出:

```
 year state_name  population  homicide
0  1995        NaN   262803276     21606
1  1996        NaN   265228572     19645
2  1997        NaN   267783607     18211
3  1998        NaN   270248003     16974
4  1999        NaN   272690813     15522 
```

如果你想知道如何找到你的 CSV 文件头，使用 **info** 函数:

```
data = pandas.read_csv('../data/estimated_crimes.csv')
print(data.info()) 
```

### 输出:

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1196 entries, 0 to 1195
Data columns (total 16 columns):
year                   1196 non-null int64
state_id               1173 non-null float64
state_abbr             1173 non-null object
state_name             1173 non-null object
population             1196 non-null int64
violent_crime          1196 non-null int64
homicide               1196 non-null int64
rape_legacy            1196 non-null int64
rape_revised           260 non-null float64
robbery                1196 non-null int64
aggravated_assault     1196 non-null int64
property_crime         1196 non-null int64
burglary               1196 non-null int64
larceny                1196 non-null int64
motor_vehicle_theft    1196 non-null int64
caveats                71 non-null object
dtypes: float64(2), int64(11), object(3) 
```

## 凶杀案总数排名前十的州

既然我们已经将数据加载到数据帧中，我们就可以开始处理数据了。我们要做的第一件事是根据凶杀案的数量对各州进行排名。

这是个坏主意。让我们来看看为什么。

这是一个非常简单的任务。首先，我们按年份查询数据框架:

```
# Get 2017 state data
# We use notnull because the state name is not null for states data = raw_data.query('year == 2017 and state_name.notnull()') 
```

接下来，我们使用 **sort_values** 对数据进行排序。
使用 **by** 属性，我们提供了一个列数组作为排序依据。在这种情况下，我们按照谋杀案的数量进行排序。

```
# Ranked By Total Homicides ranked_by_total = data.sort_values(by=['homicide'], ascending=False)
ranked_by_total = ranked_by_total.reset_index()
del ranked_by_total['index']
print(ranked_by_total.head(10)) 
```

### 输出:

```
 year      state_name  population  homicide
0  2017      California    39536653      1830
1  2017           Texas    28304596      1412
2  2017         Florida    20984400      1057
3  2017        Illinois    12802023       997
4  2017    Pennsylvania    12805537       739
5  2017            Ohio    11658609       710
6  2017         Georgia    10429379       703
7  2017        Missouri     6113532       600
8  2017  North Carolina    10273419       591
9  2017       Louisiana     4684333       582 
```

上面的凶杀案一栏是按人数排序的。

从这个简单的分类中，我们可以说加州每年的凶杀案最多。从统计数据来看，这是事实，但他们也是人口最多的州。

*人口最多的 10 个州*

```
 year      state_name  population  homicide
0  2017      California    39536653      1830
0  2017           Texas    28304596      1412
0  2017         Florida    20984400      1057
0  2017        New York    19849399       548
0  2017    Pennsylvania    12805537       739
0  2017        Illinois    12802023       997
0  2017            Ohio    11658609       710
0  2017         Georgia    10429379       703
0  2017  North Carolina    10273419       591
0  2017        Michigan     9962311       569 
```

以这种方式对各州进行排名没有意义，因为凶杀案的数量与各州的人口数量不成正比。

从凶杀案与人口的排名来看，你可以看到凶杀案的数量和人口之间有很强的相关性。

接下来，我们将根据凶杀率对各州进行排名。

## 根据凶杀案总数相对于人口规模排名的前 10 个州

现在我们将根据人口数量对各州进行排名。这将使我们对自杀率有一个更清晰的了解，例如每 100，000 人中的杀人数量。

首先，我们将使用 Pandas **apply** 函数创建一个新行。
apply 函数采用函数的名称，并将函数的结果“应用”到每一行。我们的函数叫做**人均**。

per _ capita 函数获取每一行数据，并执行计算以规范化数据。

结果是，每行都有一列，每 100，000 人中有多少人被谋杀。

```
# Ranked By Total Homicides Relative to the Population Size
# Per 100,000 people def per_capita(row):
    """Calculate the homcide rate per capita."""
    total_homicides = row['homicide']
    population = row['population']
    count = (total_homicides / population) * 100000
    return count

data['per_captia'] = data.apply(per_capita, axis=1)

# Ranked By Total Homicides ranked_by_population = data.sort_values(by=['per_captia'], ascending=False)
ranked_by_population = ranked_by_population.reset_index()
del ranked_by_population['index']
print(ranked_by_population.head(50)) 
```

最后，我们打印所有的行。

### 输出:

```
 year            state_name  population  homicide  per_captia
0   2017  District of Columbia      693972       116   16.715372
1   2017             Louisiana     4684333       582   12.424394
2   2017              Missouri     6113532       600    9.814294
3   2017                Nevada     2998039       274    9.139307
4   2017              Maryland     6052177       546    9.021547
5   2017              Arkansas     3004279       258    8.587751
6   2017                Alaska      739795        62    8.380700
7   2017               Alabama     4874747       404    8.287610
8   2017           Mississippi     2984100       245    8.210181
9   2017             Tennessee     6715984       527    7.846951
10  2017              Illinois    12802023       997    7.787832
11  2017        South Carolina     5024369       390    7.762169
12  2017            New Mexico     2088070       148    7.087885
13  2017               Georgia    10429379       703    6.740574
14  2017              Oklahoma     3930864       242    6.156407
15  2017                  Ohio    11658609       710    6.089920
16  2017               Indiana     6666818       397    5.954865
17  2017               Arizona     7016270       416    5.929076
18  2017              Kentucky     4454189       263    5.904554
19  2017          Pennsylvania    12805537       739    5.770941
20  2017        North Carolina    10273419       591    5.752710
21  2017              Michigan     9962311       569    5.711526
22  2017              Delaware      961939        54    5.613662
23  2017                Kansas     2913123       160    5.492387
24  2017              Virginia     8470020       453    5.348275
25  2017               Florida    20984400      1057    5.037075
26  2017                 Texas    28304596      1412    4.988589
27  2017         West Virginia     1815857        85    4.680985
28  2017            California    39536653      1830    4.628616
29  2017              Colorado     5607154       221    3.941393
30  2017               Montana     1050493        41    3.902929
31  2017            New Jersey     9005644       324    3.597744
32  2017                  Iowa     3145711       104    3.306089
33  2017             Wisconsin     5795483       186    3.209396
34  2017            Washington     7405743       230    3.105698
35  2017          South Dakota      869666        25    2.874667
36  2017           Connecticut     3588184       102    2.842664
37  2017              New York    19849399       548    2.760789
38  2017                Hawaii     1427538        39    2.731976
39  2017               Wyoming      579315        15    2.589265
40  2017         Massachusetts     6859819       173    2.521932
41  2017                Oregon     4142776       104    2.510394
42  2017                  Utah     3101833        73    2.353447
43  2017               Vermont      623657        14    2.244824
44  2017              Nebraska     1920076        43    2.239495
45  2017             Minnesota     5576606       113    2.026322
46  2017          Rhode Island     1059639        20    1.887435
47  2017                 Idaho     1716943        32    1.863778
48  2017                 Maine     1335907        23    1.721677
49  2017          North Dakota      755393        10    1.323814
50  2017         New Hampshire     1342795        14    1.042601 
```

## 从这个分析中我们可以推断出什么

新的列**人均**，给了我们每个州每 100，000 人中凶杀率的更准确的描述。例如，我们可以说，在路易斯安那州，每 100，000 人中，就有 2.4 人被报告谋杀。

从这一分析中，我们可以得出以下几个结论:

*   哥伦比亚特区的凶杀率最高，是人均凶杀案最多的地区之一(每 10 万人)。
*   尽管加州人口最多，但凶杀率在全国排名第 29 位。
*   新罕布什尔州的凶杀率最低。

## 结论

从这一分析中得到的教训是，按计数排序并不能说明数据的全部情况。从第一次分析来看，加州在凶杀案中排名第一，但实际上，加州的凶杀率低于其他 28 个州。这是一个如何判断数据的简单例子。

概括地说，我们使用 Pandas **read_csv** 来探索估计的 2017 年犯罪数据集。我们根据凶杀案总数对各州进行了排名。然后我们看了凶杀率，它给出了各州在凶杀案中排名的非常不同的观点。

感谢阅读！

### FBI 免责声明

> “犯罪数据浏览器中的数据代表已报告的犯罪，并不是所有已发生犯罪的详尽报告。在解释数据之前，考虑导致社区中犯罪活动和犯罪报告的各种因素非常重要。如果没有这些考虑，现有的数据可能会具有欺骗性。要考虑的因素包括人口规模和密度、经济条件、就业率、检察、司法和矫正政策、执法的行政和调查重点、公民对犯罪和治安的态度以及警察部队的有效力量。"
> 
> - [犯罪数据浏览器，检索时间 2019 年 7 月 22 日 19:45](https://crime-data-explorer.fr.cloud.gov/explorer/state/new-york/crime)

*   [有关使用 UCR 数据库的更多信息](https://ucr.fbi.gov/ucr-statistics-their-proper-use)