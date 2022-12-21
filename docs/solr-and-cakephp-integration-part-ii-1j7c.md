# Solr 和 CakePHP 集成-第二部分

> 原文：<https://dev.to/danvoyce/solr-and-cakephp-integration-part-ii-1j7c>

这是关于用 Solr 设置 CakePHP 的系列文章中的第二篇，如果你错过了第 1 部分，你可以在这里看到:[https://dev.to/locally/cakephp-and-solr-integration-417e](https://dev.to/locally/cakephp-and-solr-integration-417e)

在这篇文章中，我将分享我使用 Solarium PHP Solr 客户端库的经验，以及如何在 Solr 中执行一些基本操作，如添加、删除和搜索文档，这对于构建 CakePHP Solr 行为以在 Solr 中进行全文搜索并保持文档始终更新至关重要。

我们在本地使用 Solr 为我们的消费者参与平台 LOCALLY Engage 提供高效的[地理围栏和地理空间处理](https://locally.io/products/engage)

# 与 Solr 建立连接

使用 CakePHP 中的 Solarium PHP Solr 客户端库，这个任务很简单，您只需要几行代码就可以与 Solr 建立连接。

**首先，安装库:**

```
composer require solarium/solarium 
```

然后，创建连接:

```
use Solarium\Client;
protected $client;
public function init()
    {
        $config = [
            'endpoint' => [
                'localhost' => [
                    'host' => "SOLR-HOST",
                    'port' => "SOLR-PORT",
                    'path' => "/solr/SOLR-CORE/",
                ],
            ],
        ];
        $this->client = new Client($config);
    } 
```

# 执行搜索

基本上，要在 Solr 中执行搜索，你只需要使用几个命令，在你的查询中有很多选项可以设置，下面我将展示一个如何使用其中一些选项的例子

```
// create a client instance
$client = new Solarium\Client($config);

// get a select query instance
$query = $client->createQuery($client::QUERY_SELECT);

// set start and rows param (comparable to SQL limit) using fluent interface
$query->setStart(2)->setRows(20);

//It passes a term or "*:*" will search everything in solr
$query->setQuery($searchTerm);

// set fields to fetch (this overrides the default setting 'all fields')
$query->setFields(['id','name']);

// this executes the query and returns the result
$resultset = $client->execute($query); 
```

如果我们创建一个函数来执行搜索，并将一个选项数组作为参数传递，它应该是这样的:

```
public function search(array $options)
  {
        $query = $this->client->createSelect([
            'start' => isset($options['start']) ? $options['start'] : $this->start,
            'rows' => isset($options['limit']) ? $options['limit'] : $this->rows
        ]);
        $query->getEDisMax();
        $query->setQuery(!empty($options['match_field']) ? $options['match_field'] : '*:*');
        $query->setFields('*');
        $resultset = $this->client->select($query);
        return $resultset->getData()['response'];
} 
```

# 扩展的 DisMax (eDismax)查询解析器

这是一个有趣的选项，eDisMax 是 DisMax 查询解析器的更好版本，它允许您编写诸如 AND、OR、NOT、-和+之类的查询。也尊重所有“神奇字段”的名称，如 *val* 和 *query* 。点击此处了解更多信息。

# 处理 Solr 响应

要在 solr 中处理搜索响应，您可以:

**交互 Solr 响应对象**

```
// this executes the query and returns the result
$resultset = $client->execute($query);

// display the total number of documents found by solr
echo 'NumFound: '.$resultset->getNumFound();

// show documents using the resultset iterator
foreach ($resultset as $document) {

    echo '<hr/><table>';

    // the documents are also iterable, to get all fields
    foreach ($document as $field => $value) {
        // this converts multivalue fields to a comma-separated string
        if (is_array($value)) {
            $value = implode(', ', $value);
        }

        echo '<tr><th>' . $field . '</th><td>' . $value . '</td></tr>';
    }

    echo '</table>';
} 
```

**只返回响应:**

```
 ...
$query = $this->client->createSelect();
              ...
$resultset = $this->client->select($query);
return $resultset->getData()['response'];
Debugging Solr Response

// create a client instance
$client = new Solarium\Client($config);

// get a select query instance
$query = $client->createSelect();
$query->setQuery('*');

// add debug settings
$debug = $query->getDebug();
$debug->setExplainOther('id:MA*');

// this executes the query and returns the result
$resultset = $client->select($query);
$debugResult = $resultset->getDebug();

// display the debug results
echo '<h1>Debug data</h1>';
echo 'Querystring: ' . $debugResult->getQueryString() . '<br/>';
echo 'Parsed query: ' . $debugResult->getParsedQuery() . '<br/>';
echo 'Query parser: ' . $debugResult->getQueryParser() . '<br/>';
echo 'Other query: ' . $debugResult->getOtherQuery() . '<br/>';

echo '<h2>Explain data</h2>';
foreach ($debugResult->getExplain() as $key => $explanation) {
    echo '<h3>Document key: ' . $key . '</h3>';
    echo 'Value: ' . $explanation->getValue() . '<br/>';
    echo 'Match: ' . (($explanation->getMatch() == true) ? 'true' : 'false')  . '<br/>';
    echo 'Description: ' . $explanation->getDescription() . '<br/>';
    echo '<h4>Details</h4>';
    foreach ($explanation as $detail) {
        echo 'Value: ' . $detail->getValue() . '<br/>';
        echo 'Match: ' . (($detail->getMatch() == true) ? 'true' : 'false')  . '<br/>';
        echo 'Description: ' . $detail->getDescription() . '<br/>';
        echo '<hr/>';
    }
}

echo '<h2>ExplainOther data</h2>';
foreach ($debugResult->getExplainOther() as $key => $explanation) {
    echo '<h3>Document key: ' . $key . '</h3>';
    echo 'Value: ' . $explanation->getValue() . '<br/>';
    echo 'Match: ' . (($explanation->getMatch() == true) ? 'true' : 'false')  . '<br/>';
    echo 'Description: ' . $explanation->getDescription() . '<br/>';
    echo '<h4>Details</h4>';
    foreach ($explanation as $detail) {
        echo 'Value: ' . $detail->getValue() . '<br/>';
        echo 'Match: ' . (($detail->getMatch() == true) ? 'true' : 'false')  . '<br/>';
        echo 'Description: ' . $detail->getDescription() . '<br/>';
        echo '<hr/>';
    }
}

echo '<h2>Timings (in ms)</h2>';
echo 'Total time: ' . $debugResult->getTiming()->getTime() . '<br/>';
echo '<h3>Phases</h3>';
foreach ($debugResult->getTiming()->getPhases() as $phaseName => $phaseData) {
    echo '<h4>' . $phaseName . '</h4>';
    foreach ($phaseData as $class => $time) {
        echo $class . ': ' . $time . '<br/>';
    }
    echo '<hr/>';
} 
```

# 向 Solr 添加文档

例如，为了使索引保持最新，可以在数据库中创建或更新记录时向 Solr 添加或更新文档。让我们为它创建一个函数。

```
public function saveSearchIndex($data) 
 {
        // get an update query instance
        $update = $this->client->createUpdate();

        // create a new document for the data,
        // you can do validations before this line
        $document = $update->createDocument();
        //In my case, I am passing an array $data with the keys for my indexes. 
        foreach ($data as $key => $doc) {
            $document->{$key} = $doc;
        }
        // add the document and a commit command to the update query
        $update->addDocument($document);
        $update->addCommit();

        // this executes the query and returns the result
        return $this->client->update($update);
} 
```

# 删除文档

以非常相似的方式，您可以在 Solr 中删除文档。它有两个选项，按 id 删除或按查询删除。如果您像我一样使用一个包含多个表的数据库视图作为 Solr 源，那么可以选择通过查询删除，因为我的 id 可以在不同的表中“重复”。

下面的函数，删除索引的条件类似于“AND”条件，所以我将删除 id 为$id 和 result_type 为$result_type:
的索引

```
public function deleteSearchIndex($id, $result_type) 
{
      $update = $this->client->createUpdate();
      $update->addDeleteQuery('id:'.$id);
      $update->addDeleteQuery('result_type:'.$result_type);
      $update->addCommit();
      return $this->client->update($update);
} 
```

**相同功能按 id 删除:**

```
public function deleteSearchIndex($id) 
{
      $update = $this->client->createUpdate();
      $update->addDeleteById($id);
      $update->addCommit();
      return $this->client->update($update);
} 
```

就是这样！一旦你掌握了 Solr 的基本原理，这是非常简单的工作。

在这篇博客中，我们看到了如何:

*   使用 Solarium PHP Solr 客户端库创建与 Solr 的连接，
*   执行搜索设置查询、限制、字段和使用 eDisMax 查询解析器。
*   调试查询响应
*   处理回应
*   在 Solr 中添加和删除文档。

在下一篇博客中，我将讲述 CakePHP 与 Solr 协同工作的行为，以及如何向用户显示结果。

| ![](img/633019f04780d005bf5578ed2c4d44d3.png) | **Lorena Santana -平台开发商**在过去的+8 年中，使用不同的技术和方法进行系统分析师/Web 开发。有 ERP，CMS 和框架的经验，如 CakePHP，Bootstrap 和 Wordpress。全栈开发人员，自学，擅长数据库建模和维护，也擅长需求收集。有敏捷方法的经验，能在压力下很好地工作。实习生经验。 |