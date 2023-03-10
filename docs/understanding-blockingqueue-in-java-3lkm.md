# 理解 Java 中的 BlockingQueue

> 原文：<https://dev.to/eightbytestech/understanding-blockingqueue-in-java-3lkm>

**Java 中的 BlockingQueue 接口**最早出现在 Java 1.5 中。BlockingQueue 接口通过在 BlockingQueue 已满或为空时引入阻塞来支持流控制。BlockingQueue 的所有方法本质上都是原子的，并使用内部锁或其他形式的并发控制。

BlockingQueue 的实现是线程安全的，即试图将元素排入满队列的线程被阻塞，直到某个其他线程通过将一个或多个元素出队或完全清除队列而在队列中腾出空间。类似地，它阻塞一个试图从空队列中删除的线程，直到其他线程插入一个条目。

> BlockingQueue 不接受空值。

BlockingQueue 接口是 Java collections framework**" Java . util . concurrent "**包以及各种其他并发实用程序类(如 ConcurrentHashMap、Counting Semaphore、CopyOnWriteArrrayList 等)的一部分。

Java 提供了几种阻塞队列实现，例如:

*   ***LinkedBlockingQueue***
*   ***ArrayBlockingQueue***
*   ***PriorityBlockingQueue*T3】**
*   ***同步队列***

现在，让我们通过示例来看看这些指定的 BlockingQueue 实现，以便有一个清晰的理解。

#### LinkedBlockingQueue

**LinkedBlockingQueue** 将元素保存在链接结构(链接节点)中，并使用 *FIFO(先进先出)*顺序。如果需要，这种链接结构可以可选地具有上限。如果没有指定上限，*整数。MAX_VALUE* 作为上限。

下面是如何实例化和使用一个 **LinkedBlockingQueue** :

以下示例中的***BlockingQueueExample***类在不同的线程中启动了一个 ***生产者*** 和一个 ***消费者*** 。生产者将字符串插入 BlockingQueue，消费者将它们取出。

```
public class BlockingQueueExample {

    public static void main(String[] args) throws Exception {
        BlockingQueue<String> unbounded = new LinkedBlockingQueue<String>();
        BlockingQueue<String> bounded   = new LinkedBlockingQueue<String>(1024);

        new Thread(new Producer(bounded)).start();
        new Thread(new Consumer(bounded)).start();

        Thread.sleep(4000);
    }
} 
```

***生产者*** 在**阻塞队列**中插入字符串的类。

```
public class Producer implements Runnable {
    protected BlockingQueue queue = null;

    public Producer(BlockingQueue queue) {
        this.queue = queue;
    }

    public void run() {
        try {
            queue.put("Item #1");
            Thread.sleep(1000);
            queue.put("Item #2");
            Thread.sleep(1000);
            queue.put("Item #3");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
} 
```

***消费*** 类，它通过 ***生产者*** 类消费**阻塞队列**中插入的字符串。

```
public class Consumer implements Runnable {
    protected BlockingQueue queue = null;

    public Consumer(BlockingQueue queue) {
        this.queue = queue;
    }

    public void run() {
        try {
            System.out.println(queue.take());
            System.out.println(queue.take());
            System.out.println(queue.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
} 
```

#### ArrayBlockingQueue

**ArrayBlockingQueue** 是一个有界阻塞队列，类似于 LinkedBlockingQueue，以 *FIFO(先入先出)*的顺序在数组内部存储元素。这个 BlockingQueue 的上限是在实例化时设置的，一旦实例化就不能更改。

下面是如何实例化和使用一个 **ArrayBlockingQueue** :

```
BlockingQueue queue = new ArrayBlockingQueue(1024);

queue.put("ArrayBlockingQueue item: #1");

System.out.println(queue.take()); 
```

#### 优先级阻塞队列

与 **LinkedBlockingQueue** 和 **ArrayBlockingQueue** 不同， **PriorityBlockingQueue** 是一个无界的并发队列。它遵循与 *java.util.PriorityQueue* 类相同的排序规则，我们不能将 **null** 插入到这个队列中。

*java.lang.Comparable* 接口必须由插入到 **PriorityBlockingQueue** 中的所有元素实现。因此，这些元素根据我们在可比较的实现中决定的任何优先级进行排序。

请注意， **PriorityBlockingQueue** 并没有对具有相同优先级的元素实施任何特定的行为(compare() == 0)。同样，当我们从 PriorityBlockingQueue 获得迭代器时，迭代器不能保证按照优先级顺序迭代元素。

下面是如何实例化和使用一个**优先级阻塞队列** :

```
BlockingQueue queue = new PriorityBlockingQueue();

//String class implements java.lang.Comparable
queue.put("PriorityBlockingQueue item: #1");

System.out.println(queue.take()); 
```

#### 同步队列

**SynchronousQueue** 是一个内部只能包含一个元素的队列，它只支持两个操作: ***put()*** 和 ***take()*** 。

这两个操作都是阻塞的，即，如果一个线程试图将一个元素插入到队列中，它会被阻塞，直到另一个线程从队列中取出该元素。同样，如果一个线程试图获取一个元素，而当前没有元素存在，那么该线程将被阻塞，直到一个线程将一个元素插入到队列中。

虽然 **SynchronousQueue** 有一个队列接口，但是我们应该把它看作是两个线程之间的单个元素的交换点。

帖子[理解 Java 中的阻塞队列](https://developersjournal.in/understanding-blockingqueue-in-java/)最早出现在[开发者杂志](https://developersjournal.in)上。