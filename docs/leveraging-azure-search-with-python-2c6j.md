# 利用 Python 进行 Azure 搜索

> 原文：<https://dev.to/documentednerd/leveraging-azure-search-with-python-2c6j>

所以最近我一直在做一个兼职项目，展示 Azure 中关于 PaaS 服务的一些功能，我参与最多的是 Azure Search。

所以让我们从一个显而易见的问题开始，Azure 搜索是什么？Azure Search 是一个平台即服务产品，允许以可扩展的方式将搜索作为云解决方案的一部分来实现。

下面是一些关于“什么是 Azure 搜索？”

*   [什么是 Azure 搜索？](https://docs.microsoft.com/en-us/azure/search/search-what-is-azure-search)
*   [使用 Azure Search 抓取 SQL 数据库](https://docs.microsoft.com/en-us/azure/search/search-indexer-tutorial)
*   [使用 Azure Search 搜索 Azure Blobs】](https://docs.microsoft.com/en-us/azure/search/search-semi-structured-data)
*   [使用 Azure Search 索引多个数据源](https://docs.microsoft.com/en-us/azure/search/tutorial-multiple-data-sources)
*   [使用 Azure Search 进行全文搜索](https://docs.microsoft.com/en-us/azure/search/search-lucene-query-architecture)
*   [认知搜索简介](https://docs.microsoft.com/en-us/azure/search/cognitive-search-concept-intro)

第一部分是如何创建搜索服务，我发现最简单的方法是通过 CLI 创建:

```
az search service create --name {name} --resource-group {group} --location {location} 
```

所以在你创建了 Azure 搜索服务之后，下一步就是创建所有需要的部分。为此，我一直在通过 Python 使用 REST API 来管理这些元素，所以您将在这里看到这些代码。

*   创建数据源
*   创建索引
*   创建索引器
*   运行索引器
*   获取索引器状态
*   运行搜索

## 项目描述:

在这篇文章中，我将构建一个搜索索引，通过芝加哥数据门户收集数据，通过 API 提供统计数据和公共信息。这个解决方案将 API 中的数据放入 cosmos db，使信息可以搜索。我只使用公开消费信息作为这一部分。门户网站上的信息可以在[这里](https://data.cityofchicago.org/browse)找到。

## 创建数据源

所以，任何搜索讨论的第一部分，就是你需要有一个可以搜索的数据源。没有它走不远。所以问题就变成了，你要搜索什么。Azure Search 支持各种各样的数据源，出于讨论的目的，我将它指向 Cosmos Db。目的是搜索一个 cosmos db 的内容，以确保我可以取回相关的条目。

下面是我用来创建搜索数据源的代码:

```
import jsonimport requestsfrom pprint import pprint#The url of your search serviceurl = 'https://[Url of the search service]/datasources?api-version=2017-11-11'print(url)#The API Key for your search serviceapi\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}data = { 'name': 'cosmos-crime', 'type': 'documentdb', 'credentials': {'connectionString': '[connection string for cosmos db]'}, 'container': {'name': '[collection name]'}}data = json.dumps(data)print(type(data))response = requests.post(url, data=data, headers=headers)pprint(response.status\_code) 
```

要获得 API 密钥，您需要管理密钥，可以使用以下命令找到它:

```
az search admin-key show --service-name [name of the service] -g [name of the resource group] 
```

运行以上程序后，您将创建一个用于搜索的数据源。

## 创建索引

一旦有了上面的数据源，下一步就是创建一个索引。这个索引是 Azure Search 将你的数据映射到的内容，也是它将来实际执行搜索的方式。因此，最终把这当成你的搜索完成后的格式。要创建索引，使用下面的代码:

```
import jsonimport requestsfrom pprint import pprinturl = 'https://[Url of the search service]/indexes?api-version=2017-11-11'print(url)api\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}data = { "name": "crimes", "fields": [{"name": "id", "type": "Edm.String", "key":"true", "searchable": "false"}, {"name": "iucr","type": "Edm.String", "searchable":"true", "filterable":"true", "facetable":"true"}, {"name": "location\_description","type":"Edm.String", "searchable":"true", "filterable":"true"}, {"name": "primary\_description","type":"Edm.String","searchable":"true","filterable":"true"}, {"name": "secondary\_description","type":"Edm.String","searchable":"true","filterable":"true"}, {"name": "arrest","type":"Edm.String","facetable":"true","filterable":"true"}, {"name": "beat","type":"Edm.Double","filterable":"true","facetable":"true"}, {"name": "block", "type":"Edm.String","filterable":"true","searchable":"true","facetable":"true"}, {"name": "case","type":"Edm.String","searchable":"true"}, {"name": "date\_occurrence","type":"Edm.DateTimeOffset","filterable":"true"}, {"name": "domestic","type":"Edm.String","filterable":"true","facetable":"true"}, {"name": "fbi\_cd", "type":"Edm.String","filterable":"true"}, {"name": "ward","type":"Edm.Double", "filterable":"true","facetable":"true"}, {"name": "location","type":"Edm.GeographyPoint"}] }data = json.dumps(data)print(type(data))response = requests.post(url, data=data, headers=headers)pprint(response.status\_code) 
```

使用上面的代码，我已经确定了最终产品的不同数据类型，这些都映射到为 azure search 确定的数据类型。支持的数据类型可以在[这里](https://docs.microsoft.com/en-us/rest/api/searchservice/supported-data-types)找到。

值得一提的是，上面还有其他关键属性需要考虑:

*   facetable:这表示数据是否可以分面。例如，在 Yelp 中，如果我搜索费用，所有餐馆都有一个“$”到“$$$”的评级，我希望能够根据这个方面对结果进行分组。
*   可过滤:这表示数据集是否可以基于这些值进行过滤。
*   可搜索:这表示是否对字段执行全文搜索，并且受限于可用于执行搜索的不同类型的数据。

## 创建索引器

所以下一步是创建索引器。索引器的目的是完成真正的工作。索引器负责执行以下操作:

*   连接到数据源
*   提取数据，并将其转换成适合索引的格式
*   执行任何数据转换
*   管理拉入没有正在进行的数据

```
import jsonimport requestsfrom pprint import pprinturl = 'https://[Url of the search service]/indexers?api-version=2017-11-11'print(url)api\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}data = { "name": "cosmos-crime-indexer", "dataSourceName": "cosmos-crime", "targetIndexName": "crimes", "schedule": {"interval": "PT2H"}, "fieldMappings": [{"sourceFieldName": "iucr", "targetFieldName": "iucr"}, {"sourceFieldName": "location\_description", "targetFieldName": "location\_description"}, {"sourceFieldName": "primary\_decsription", "targetFieldName": "primary\_description"}, {"sourceFieldName": "secondary\_description", "targetFieldName": "secondary\_description"}, {"sourceFieldName": "arrest", "targetFieldName": "arrest"}, {"sourceFieldName": "beat", "targetFieldName": "beat"}, {"sourceFieldName": "block", "targetFieldName": "block"}, {"sourceFieldName": "casenumber", "targetFieldName": "case"}, {"sourceFieldName": "date\_of\_occurrence", "targetFieldName": "date\_occurrence"}, {"sourceFieldName": "domestic", "targetFieldName": "domestic"}, {"sourceFieldName": "fbi\_cd", "targetFieldName": "fbi\_cd"}, {"sourceFieldName": "ward", "targetFieldName": "ward"}, {"sourceFieldName": "location", "targetFieldName":"location"}]}data = json.dumps(data)print(type(data))response = requests.post(url, data=data, headers=headers)pprint(response.status\_code) 
```

您会注意到，对于每个字段，都分配了两个属性:

*   targetFieldName:这是索引中作为目标的字段。
*   sourceFieldName:这是根据数据源的字段名称。

## 运行索引器

一旦创建了索引器，下一步就是运行它。这将导致索引器将数据拉入索引:

```
import jsonimport requestsfrom pprint import pprinturl = 'https://[Url of the search service]/indexers/cosmos-crime-indexer/run/?api-version=2017-11-11'print(url)api\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}reseturl = 'https://[Url of the search service]/indexers/cosmos-crime-indexer/reset/?api-version=2017-11-11'resetResponse = requests.post(reseturl, headers=headers)response = requests.post(url, headers=headers)pprint(response.status\_code) 
```

通过触发“运行”索引器，索引器将加载索引。

## 获取步进器状态

现在，根据数据源的大小，这个索引过程可能需要一些时间，所以我想提供一个 rest 调用，让您可以获得索引器的状态。

```
import jsonimport requestsfrom pprint import pprinturl = 'https://[Url of the search service]/indexers/cosmos-crime-indexer/status/?api-version=2017-11-11'print(url)api\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}response = requests.get(url, headers=headers)index\_list = response.json()pprint(index\_list) 
```

这将为您提供索引器的状态，以便您可以发现它何时完成。

## 运行搜索

最后，如果你想确认搜索正在进行，你可以执行以下操作:

```
import jsonimport requestsfrom pprint import pprinturl = 'https://[Url of the search service]/indexes/crimes/docs?api-version=2017-11-11'print(url)api\_key = '[api key for the search service]'headers = { 'Content-Type': 'application/json', 'api-key': api\_key}response = requests.get(url, headers=headers)index\_list = response.json()pprint(index\_list) 
```

这将带回搜索的结果。这将返回所有内容，因为这是一个空字符串搜索。

我希望这对你配置 Azure 搜索有所帮助，祝你搜索愉快:)！