# 罗布。商务 9.2

> 原文：<https://dev.to/robearlam/rob-commerce-9-2-1ah0>

这周我终于有时间升级我的 Rob 了。商务解决方案 [Sitecore 体验商务 9.2](https://dev.sitecore.net/Downloads/Sitecore_Commerce/92/Sitecore_Experience_Commerce_92_Initial_Release.aspx) 。该软件大约在一个月前发布，并期待看到新版本的运行。

## 列表修改

作为 XC 9.2 引入的性能改进的一部分，系统功能有了一些变化，其中一个让我在升级过程中出错的是对列表处理方式的改变。我的解决方案包括一个定制的产品比较功能，它作为一个列表存储在 **SharedEnvironments** 数据库中。升级解决方案后，此功能停止工作，我可以看到在**商业实体**表中创建了列表，但是每当我试图检索列表时，它总是为空，并且没有记录任何错误。在从产品团队的 Geovannia 那里得到一些帮助后，很明显这是由于 by list 现在被存储在了错误的表中。这是一个包含目录数据的列表，但它没有存储在 **CatalogLists** 表中，而是存储在 **CommerceLists** 表中。

为了解决这个问题，我必须将我的列表定义添加到**插件中。SQL . sharding . policy set-1 . 0 . 0 . JSON**定义。因此，我的目录列表的分片策略现在看起来像:

```
{
    "$type": "Sitecore.Commerce.Plugin.SQL.ListShardingPolicy, Sitecore.Commerce.Plugin.SQL",
    "Expressions": {
        "$type": "System.Collections.Generic.List`1[[System.String, mscorlib]], mscorlib",
        "$values": [
            "^List-CatalogItems.*?$",
            "^List-Catalogs.*?$",
            "^List-Categories.*?$",
            "^List-SellableItems.*?$",
            "^List-Categories.*?$",
            "^List-SellableItems.*?$",
            "^List-PurgeCatalogs.*?$",
            "^List-PurgeCategories.*?$",
            "Entity-Catalog.*?$",
            "Entity-Category.*?$",
            "Entity-SellableItem.*?$",
            "^List-Entity-ProductCompare.*?$"
        ]
    },
    "TableName": "CatalogLists"
} 
```

你可以看到值数组中的最后一行现在将确保我的产品比较列表实体将存储在 **CatalogLists** 表中，而不是 **CommerceLists** 表中。一旦这个改变被引导到引擎中，那么一切又开始工作了，成功了！

## 从独角兽变为 TDS

作为升级过程的一部分，我还将解决方案从使用 Unicorn 的序列化框架改为使用 Sitecore TDS。我已经很多年没有使用 TDS 了，我很想看看自从我上次使用它以来，这个软件是如何发展的。总的来说，更改进行得非常顺利，它只涉及到我从存储库中删除 Unicorn 引用和序列化文件，然后设置 TDS 项目。该产品自上次使用以来没有经历过任何大规模的变化，但是已经引入了一些不错的东西，如快速推送和一次同步所有项目的能力。我列表中的下一个是考虑使用新的 YML 序列化特性。

## 结论

升级到 XC92 是一件值得做的事情，纯粹是因为它给你带来的性能提升。这还没有考虑到对营销自动化和 EXM 功能的改进——这也是我清单上需要更详细调查的内容。正如我遇到的列表问题一样，引入了一些其他更改来提供已经交付的性能改进，例如，如何处理数据本地化的差异，这可以在这个[堆栈溢出](https://sitecore.stackexchange.com/questions/14250/add-localization-to-entity)问题中看到。因此，虽然升级时需要考虑一些变化，但性能提升肯定值得付出努力！