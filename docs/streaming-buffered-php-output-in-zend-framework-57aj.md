# Zend 框架中的流式缓冲 PHP 输出

> 原文：<https://dev.to/ethanbray/streaming-buffered-php-output-in-zend-framework-57aj>

我最近在开发一个功能，需要我从 Elasticsearch 中检索大量数据(7 个数字文档计数)，修改数据，然后输出一个 CSV 文件。这个特性需要是高性能的，并且不会对应用程序 EC2 实例的内存使用产生负面影响。

该功能的最初实现是检索所有文档，将它们存储在内存中，然后在磁盘上创建一个文件提供给用户。这导致内存耗尽，并占用生产实例上的大量磁盘空间。

这个伪代码代表了我们最初的实现:

```
class ExportController() {
    public function exportAction(): Stream {
        $exportQuery = $this->params()->fromPost('query');
        $this->dataExporter->export($exportQuery);

        $response        = $this->getResponse();
        $responseHeaders = $response
            ->getHeaders()
            ->addHeaders([
                'Content-Description'       => 'File Transfer',
                'Content-Type'              => 'text/csv',
                'Content-Transfer-Encoding' => 'binary',
                'Expires'                   => 0,
                'Cache-Control'             => 'must-revalidate',
                'Pragma'                    => 'public',
            ]);
        $response->setHeaders($responseHeaders);
        $response->setContent($fileContents);
        return $response;
    }
}

class DataExporter {
    public function export($exportQuery)
    {
        // Get all of the results
        $results = $this->elasticsearchService->getData($exportQuery);

        // Iterate through data and output to CSV file
        $filePointer = fopen('/tmp/data.csv', 'wb');
        foreach ($results as $result) {
            fputcsv($filePointer, $result, ',');
        }
    }
} 
```

[`fputcsv`](https://www.php.net/manual/en/function.fputcsv.php) 将一个`resource`作为它的第一个参数，包括一个指向 PHP I/O 流的资源，比如`php://output`。通过用`php://output`替换`/tmp/data.csv`，我们不再需要在服务器上创建 CSV 文件。

既然我们将数据直接写入输出流，我们需要在刷新缓冲区之前发送正确的头:

```
class ExportController() {
    public function exportAction(): Stream {
        $exportQuery = $this->params()->fromPost('query');
        $this->dataExporter->export($exportQuery);
    }
}

class DataExporter {
    public function export($exportQuery): void
    {
        // Get all of the results
        $results = $this->elasticsearchService->getData($exportQuery);

        // Iterate through data and output to CSV file
        ob_start();
        $outputPointer = fopen('php://output', 'wb');
        foreach ($results as $result) {
            fputcsv($outputPointer, $result, ',');
        }

        // Close our file pointer, send the file headers and flush our output buffer
        fclose($filePointer);
        header('Content-Description: File Transfer');
        header('Content-Type: text/csv');
        header('Content-Transfer-Encoding: binary');
        header('Expires: 0');
        header('Cache-Control: must-revalidate');
        header('Pragma: public');
        ob_end_flush();
        exit();
    }
} 
```

我们使用`exit()`来避免`headers already sent`错误，因为 Zend Framework 将尝试向用户发送响应和更多的头。

如果我们要为`export`方法编写一个测试，我们很快就会遇到这样的问题:我们对`exit()`的使用结束了我们的 PHPUnit 进程。除了猴子给`exit()`函数打补丁，我们对此无能为力。除了移除`exit()`之外。在我们的`export`方法中，我们可以创建一个`Stream`响应并将其返回给我们的控制器，而不是刷新缓冲区。控制器然后将把`Stream`返回给 Zend 框架，Zend 框架将把它发送给我们的用户。

```
class ExportController() {
    public function exportAction(): Stream {
        $exportQuery = $this->params()->fromPost('query');
        return $this->dataExporter->export($exportQuery);
    }
}

class DataExporter {
    public function export($exportQuery): Stream
    {
        // Get all of the results
        $results = $this->elasticsearchService->getData($exportQuery);

        // Create our Stream
        $outputPointer = fopen('php://output', 'wb');
        $stream = new Stream();
        $stream->setStream($outputPointer);
        $stream->setHeaders((new Headers())->addHeaders([
            'Content-Description'       => 'File Transfer',
            'Content-Type'              => 'text/csv',
            'Content-Transfer-Encoding' => 'binary',
            'Expires'                   => '0',
            'Cache-Control'             => 'must-revalidate',
            'Pragma'                    => 'public',
        ]);

        // Iterate through data and output to CSV file
        foreach ($results as $result) {
            fputcsv($outputPointer, $result, ',');
        }

        return $stream;
    }
} 
```

当控制器返回一个响应时，Zend MVC 发出一个`SendResponseEvent`事件，几个 Zend 框架监听器监听这个事件。对于我们可能返回的每种类型的响应，都有一个监听器可用。在我们的例子中，`StreamResponseSender`将负责向前发送我们的响应。

*   `Zend\Mvc\SendResponseListener\PhpEnvironmentResponseSender`优先级为`-1000`
*   `Zend\Mvc\SendResponseListener\ConsoleResponseSender`优先级为`-2000`
*   `Zend\Mvc\SendResponseListener\SimpleStreamResponseSender`优先级为`-3000`

```
class SimpleStreamResponseSender extends AbstractResponseSender
{
    public function sendStream(SendResponseEvent $event)
    {
        if ($event->contentSent()) {
            return $this;
        }
        $response = $event->getResponse();
        $stream   = $response->getStream();
        fpassthru($stream);
        $event->setContentSent();
    }

    public function __invoke(SendResponseEvent $event)
    {
        $response = $event->getResponse();
        if (! $response instanceof Stream) {
            return $this;
        }
        $this->sendHeaders($event);
        $this->sendStream($event);
        $event->stopPropagation(true);
        return $this;
    }
} 
```

现在很遗憾，`fpassthru`不支持使用`php://output`的资源。试图使用`StreamResponseSender`将导致一个关于无效资源的错误。用`ob_end_flush`替换`fpassthru`就是让`SimpleStreamResponseSender`工作的全部要求。

现在我们需要创建我们自己的响应发送器来使用`php://output`处理这些`Stream`响应。我们还需要区分使用`php://output`和标准`Stream`的响应。

```
class BufferedStream extends Stream {
    public function __construct($filePointer)
    {
        $this->setHeaders((new Headers())->addHeaders([
            'Content-Description'       => 'File Transfer',
            'Content-Type'              => 'text/csv',
            'Content-Transfer-Encoding' => 'binary',
            'Expires'                   => '0',
            'Cache-Control'             => 'must-revalidate',
            'Pragma'                    => 'public',
        ]));
    }
}

class BufferedStreamResponseSender extends AbstractResponseSender
{
    public function sendStream(SendResponseEvent $event): self
    {
        if ($event->contentSent()) {
            return $this;
        }

        // fpassthru does not work with php://output so we have to flush the buffer instead
        ob_end_flush();
        $event->setContentSent();
        return $this;
    }

    public function __invoke(SendResponseEvent $event)
    {
        $response = $event->getResponse();
        if (! $response instanceof BufferedStream) {
            return $this;
        }

        $this->sendHeaders($event);
        $this->sendStream($event);
        $event->stopPropagation(true);
        return $this;
    }
} 
```

现在我们有了监听器和新的`BufferedStream`，我们只需要更新我们的`DataExporter`来使用正确的响应，并将监听器连接到`SendResponseEvent`。

```
class ExportController() {
    public function exportAction(): BufferedStream {
        $exportQuery = $this->params()->fromPost('query');
        return $this->dataExporter->export($exportQuery);
    }
}

class DataExporter {
    public function export($exportQuery): BufferedStream
    {
        // Get all of the results
        $results = $this->elasticsearchService->getData($exportQuery);

        // Create our Stream
        $outputPointer = fopen('php://output', 'wb');
        $stream = new BufferedStream();
        $stream->setStream($outputPointer);

        // Iterate through data and output to CSV file
        foreach ($results as $result) {
            fputcsv($outputPointer, $result, ',');
        }

        return $stream;
    }
}

class Module implements ConfigProviderInterface
{
    /**
     * @inheritdoc
     */
    public function getConfig()
    {
        return include __DIR__ . '/../config/module.config.php';
    }

    /**
     * @inheritdoc
     */
    public function onBootstrap(EventInterface $e)
    {
        /** @var EventManager $eventManager */
        $eventManager  = $e->getApplication()->getEventManager();
        $eventManager->attach(SendResponseEvent::EVENT_SEND_RESPONSE, new BufferedStreamResponseSender(), 1);
    }
} 
```

我们现在不再需要将数据处理到中间的
文件中。数据被处理，然后直接传输到 PHP 的缓冲
输出。

Elasticsearch 提供了用于处理大量数据的滚动 API 。它的操作方式类似于传统数据库中的游标。通过使用 Scroll API 将我们的单个`getData`调用替换为一个循环，我们可以最小化一次存储在内存中的数据量。

```
class DataExporter {
    public function export($exportQuery): BufferedStream
    {
        // Create our Stream
        $outputPointer = fopen('php://output', 'wb');
        $stream = new BufferedStream();
        $stream->setStream($outputPointer);

        // Start our scroll
        $resultSet = $this->elasticsearchService->startScroll($exportQuery);
        $this->writeDataToResource($outputPointer, $resultSet);

        // Continue the scroll till we have no further results
        while (count($resultSet->getResults()) !== 0) {
            $resultSet = $this->elasticsearchService->continueScroll($resultSet);
            $this->writeDataToResource($outputPointer, $resultSet);
        }

        return $stream;
    }

    private function writeDataToResource($outputPointer, ResultSet $resultSet): void
    {
        $results = $resultSet->getResults();
        foreach ($results as $result) {
            fputcsv($outputPointer, $result, ',');
        }
    }
} 
```

通过使用 streams 和 Scroll API，我们减少了内存使用，并完全消除了文件使用。