# 原则:如何从回滚的事务中恢复

> 原文：<https://dev.to/ragezbla/doctrine-how-to-recover-from-rolled-back-transaction-47o4>

我们都希望只处理快乐的事情，但不幸的是，在现实中，错误发生了，我们必须处理它们。

发生错误后清理混乱的一种方法是数据库事务，尤其是回滚。事务是一个强大的工具，可以防止我们的应用程序让数据库处于未知或崩溃的状态。

遗憾的是，大多数 ORM 并没有很好地处理回滚。

让我们看一个真实世界的小例子。我们的应用程序需要向客户发送一些电子邮件，并记录该电子邮件已发送。处理是通过一次对一个客户执行操作来批量完成的。

```
 public function sendWeeklyNewsletter(): void
{
    $customers = $repository->findAll();

    foreach ($customers as $customer) {
        try {
            $entityManager->transactional(
                function () use ($customer) {
                    $this->sendWeeklyNewsletter($customer);
                    $this->recordEmailNotification($customer);
                }
            );
        } catch (EmailNotTransmittedException $e) {

        }
    }
} 
```

假设第一封电子邮件无法发送(随机中断、电子邮件不正确等)，您会认为它应该工作，`transactional`将回滚事务，并继续处理下一个客户。

不幸的是，事实并非如此，你会得到这个异常`PHP Fatal error: Uncaught Doctrine\ORM\ORMException: The EntityManager is closed.`的奖励。

看一看`EntityManager::transactional`的代码就能解释为什么。

```
 /**
     * {@inheritDoc}
     */
    public function transactional($func)
    {
        if (!is_callable($func)) {
            throw new \InvalidArgumentException('Expected argument of type "callable", got "' . gettype($func) . '"');
        }

        $this->conn->beginTransaction();

        try {
            $return = call_user_func($func, $this);

            $this->flush();
            $this->conn->commit();

            return $return ?: true;
        } catch (Throwable $e) {
            $this->close();
            $this->conn->rollBack();

            throw $e;
        }
    } 
```

基本上是因为处理回滚很复杂。教条维护者选择在回滚发生时关闭实体管理器。[文件](https://www.doctrine-project.org/projects/doctrine-orm/en/current/reference/transactions-and-concurrency.html#exception-handling)中包含这样的警告:

> 此过程的结果是，EntityManager 的所有以前管理或删除的实例都被分离。分离对象的状态将是事务回滚时的状态。对象的状态不会回滚，因此对象现在与数据库不同步。应用程序可以继续使用分离的对象，知道它们的状态可能不再准确。如果您打算在异常发生后启动另一个工作单元，您应该使用新的 EntityManager。

TD；发生回滚后，不能使用实体管理器。

现在，如果我们不能从错误中恢复，我们该如何进行批处理呢！？！？

[![oh dear jesus](img/261dcbed9eae47d79fcb5327f3a9699a.png)](https://i.giphy.com/media/3o6ZtpcXSzZg7jCJI4/giphy.gif)

每次回滚发生时，我们都需要创建一个新的实体管理器。如果您使用容器，这可能会变得复杂，因为它将在任何地方共享相同的实体管理器实例。

请看`AbstractManagerRegistry`，这个注册中心有一个`resetManager`方法，允许创建一个新的实体管理器。

无论如何，如果你使用 symfony 框架，doctrine bridge 捆绑包提供了一个 doctrine 注册表的实现，并允许你重置实体管理器。我很确定其他框架集成应该提供相同的特性。

所以我们的代码变成:

```
 public function sendWeeklyNewsletter(): void
{
    $customers = $repository->findAll();

    foreach ($customers as $customer) {
        try {
            $entityManager->transactional(
                function () use ($customer) {
                    $this->sendWeeklyNewsletter($customer);
                    $this->recordEmailNotification($customer);
                }
            );
        } catch (EmailNotTransmittedException $e) {
            $this->registry->resetManager();
        }
    }
} 
```

细心的读者可能发现了我们逻辑中的一个问题。实体管理器已经传递给我们的类，所以它不能被交换。事实上，symfony doctrine bundle 依赖于 Ocramius 编写的精彩的[代理管理器](https://github.com/Ocramius/ProxyManager)库来包装 EntityManager 对象，因此它可以被动态交换。

[![smart](img/d8f47a2f9d2fb5dc4b8ef5efa8878ec6.png)](https://i.giphy.com/media/d3mlE7uhX8KFgEmY/giphy.gif)

无论如何，你可以看看注册表的[实现](https://github.com/symfony/doctrine-bridge/blob/master/ManagerRegistry.php#L41)。

快乐批量加工！