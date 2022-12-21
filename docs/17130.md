# 在铁锈上使用螺纹(第 1 部分)

> 原文：<https://dev.to/dandyvica/using-threads-on-rust-part-1-2gld>

Rust 的一个好处是，除了安全之外，还能使用线程而不会冒数据竞争的风险。编译器应该能检测出代码中的这种缺陷。数据竞赛的定义非常明确:

> 当两个或多个线程同时访问同一个内存位置，并且其中至少有一个访问是写操作时，就会发生数据争用(参考。来自 Oracle 文档的定义)

我的挑战是想象一个简单的用例场景，其中线程可以增加明确的优势(在某种程度上)。将向量的元素相加是一个完美的方法，因为它可以被分解成独立的任务，彼此独立。在这种情况下，所有启动的线程可能只负责对向量元素的一部分求和。

因为我希望对任何类型 *T* 都实现这一点，所以这就要求这种类型的加法既是可交换的(因为线程可能以不确定的方式启动)又是关联的。它适用于复数、实数和整数，但不适用于字符串，例如，如果我们用连接操作来识别求和。

我还想用这个例子作为在 Rust 上使用线程的教育实验，但是没有任何额外的板条箱。我的意见(这在 Rustaceans 中是有争议的)是，对于这样简单的任务，这应该在核心语言中，而不是转移到一个附加的库中。以 *regex* 板条箱为例:使用它需要下载和编译另外 8 个板条箱:

```
 Compiling memchr v2.2.0
   Compiling regex v1.1.7
   Compiling lazy_static v1.3.0
   Compiling ucd-util v0.1.3
   Compiling utf8-ranges v1.0.3
   Compiling thread_local v0.3.6
   Compiling regex-syntax v0.6.7
   Compiling aho-corasick v0.7.3 
```

Enter fullscreen mode Exit fullscreen mode

这一立场似乎在使用 Rust 的人群中并不普遍，但我坚信这是一条正确的道路。希望在未来，一些板条箱会被整合到 *std* 中。

求和的整个过程如下:

*   根据线程的数量，每个启动的线程将负责部分求和
*   每个线程将使用通道将其结果发送给主线程
*   主线程将协调并计算部分总和的总和，该总和等于总总和

## 简单的线程实验

下面是一个简单但丰富的线程实验。只需生成 n 个线程就可以打印出每个线程中移动的值:

```
// just print out the value copied in each thread
fn thread_test<T>(val: T, nb_threads: usize) -> Vec<thread::JoinHandle<()>>
where
    T: 'static + Send + Sync + Copy + Debug,
{
    let mut v = Vec::new();

    for _ in 0..nb_threads {
        v.push(thread::spawn(move || {
            println!(
                "Value passed in thread {:?} = {:?}",
                thread::current().id(),
                val
            )
        }));
    }
    v
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有两件事很关键:

*   “T0”静态生存期，它指示由这种类型的对象指向的引用持续多长时间
*   *复制*线程必须将*值*传递给一行中的每个线程。

但并不是所有类型都是可复制的: *String* 不是。

之前的 *fn* 可以这样用:

```
for th in thread_test(3.14f32, 2) {
    th.join();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 版本不带任何附加板条箱

我不得不承认这很难实现，特别是与其他语言如 D 或臃肿的 C++相比。主要的好处是学习如何使用线程，使用通道和静态生存期在线程之间进行通信。

以下是代码:

```
use std::fmt::Debug;
use std::ops::AddAssign;
use std::sync::mpsc;
use std::sync::Arc;
use std::thread;

// parall summation of a Vector of T elements
fn parallel_sum<T>(v: Vec<T>, nb_threads: usize) -> T
where
    T: 'static + Send + Sync + Debug + AddAssign + Default + Copy,
{
    // this vector will hold created threads
    let mut threads = Vec::new();

    // need to arced the vector to share it
    let arced = Arc::new(v);

    // this channel will be use to send values (partial sums) for threads
    let (sender, receiver) = mpsc::channel::<T>();

    // create requested number of threads
    for thread_number in 0..nb_threads {
        // increment ref count, will be moved into the thread
        let arced_cloned = arced.clone();

        // each thread gets its invidual sender
        let thread_sender = sender.clone();

        // create thread and save ID for future join
        let child = thread::spawn(move || {
            // initialize partial sum
            let mut partial_sum: T = T::default();

            // this line doesn't compile:
            // partial_sum = arced_cloned.into_iter().sum();

            // trivial old style loop: using the sum() method didn't help
            for i in 0..arced_cloned.len() {
                // depending on index, add it
                if i % nb_threads == thread_number {
                    partial_sum += *arced_cloned.get(i).unwrap();
                }
            }

            // send our result to main thread
            thread_sender.send(partial_sum).unwrap();

            // print out partial sum
            println!(
                "thread #{}, partial_sum of modulo {:?} = {:?}",
                thread_number, thread_number, partial_sum
            );
        });

        // save thread ID
        threads.push(child);
    }

    // wait for children threads to finish
    for child in threads {
        let _ = child.join();
    }

    let mut total_sum = T::default();
    for _ in 0..nb_threads {
        // main thread receives the partial sum from threads
        let partial_sum = receiver.recv().unwrap();

        // and get the final total sum
        total_sum += partial_sum
    }

    total_sum
} 
```

Enter fullscreen mode Exit fullscreen mode

这个版本可能是次优的。可以为每个线程分配一个部分范围，以便仅在该范围上应用求和，从而节省每个索引上的模运算。

在下面的文章中，我将使用特定的板条箱来实现(希望)相同的结果。

希望这有所帮助！

> 照片由 Héctor J. Rivas 在 Unsplash 上拍摄