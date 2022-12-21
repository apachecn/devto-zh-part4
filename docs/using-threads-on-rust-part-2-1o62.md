# 在铁锈上使用螺纹(第 2 部分)

> 原文：<https://dev.to/dandyvica/using-threads-on-rust-part-2-1o62>

继续我之前关于使用生锈线的文章，现在是时候通过使用专用板条箱来使用更生锈的方法了。在我朋友的帮助下。给甲壳虫乐队。)，我在这里得到 Rust 用户群跟帖的有用建议:[https://users . Rust-lang . org/t/help-for-my-parallel-sum/29253](https://users.rust-lang.org/t/help-for-my-parallel-sum/29253)。

出于许多原因，这似乎是使用线程时的必由之路。起初，我有点不愿意使用外部机箱来进行这种基本的线程编程，但是现在这已经成为一种趋势，我已经尝试过了。但是我没有简单地计算矢量元素的总和，而是用一个更通用的函数代替了总和:

```
// function type which will run in each thread
type ChunkTask<'a, T> = fn(&'a [T]) -> T; 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的函数将接受一个向量切片并返回一个 *T* 元素。它可以是任何东西:求和，平方和，乘积，你能想到的。为了应用这种生锈的惯用方式，我创造了一种特殊的特质:

```
//---------------------------------------------------------------------------------------
// trait to call its fn directly from a Vec<T>
//---------------------------------------------------------------------------------------
pub trait ParallelTask<T> {
    // distribute work among threads. As a result, we'll got a Vec<T> which is the result of thread tasks
    fn parallel_task<'a>(&'a self, nb_threads: usize, computation: ChunkTask<'a, T>) -> Vec<T>
    where
        T: 'a + Send + Sync;
} 
```

Enter fullscreen mode Exit fullscreen mode

*parallel_task* 函数将在每个任务上调用*计算*函数，其大小取决于线程数量。最后，返回计算出的 *T* 元素的向量。请注意，由于操作系统线程的性质，这些元素推入的顺序是不确定的。

诀窍是使用*横梁*板条箱，它是为了减轻 Rust 1.0 之前*线程::作用域* API 中的一些缺陷而创建的。*作用域*环境允许更灵活地使用和创建线程:

```
impl<T> ParallelTask<T> for [T] {
    fn parallel_task<'a>(&'a self, nb_threads: usize, computation: ChunkTask<'a, T>) -> Vec<T>
    where
        T: 'a + Send + Sync,
    {
        // figure out the right size for the number of threads, rounded up
        let chunk_size = (self.len() + nb_threads - 1) / nb_threads;

        // create the channel to be able to receive partial sums from threads
        let (sender, receiver) = mpsc::channel::<T>();

        // create empty vector which will receive all computed valued from children threads
        let mut values: Vec<T> = Vec::new();

        crossbeam::scope(|scope| {
            // create threads: each thread will get the partial sum
            for chunk in self.chunks(chunk_size) {
                // each thread gets its invidual sender
                let thread_sender = sender.clone();

                // spawn thread
                scope.spawn(move |_| {
                    // call dedicated specialized fn
                    let partial_sum: T = computation(chunk);

                    // send it through channel
                    thread_sender.send(partial_sum).unwrap();
                });
            }

            // drop our remaining sender, so the receiver won't wait for it
            drop(sender);

            // sum the results from all threads
            values = receiver.iter().collect();
        })
        .unwrap();

        values
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以实现专门的功能。一旦 *Sum* 和 *Prod* 特征被实现，下面这些是可能的:

```
// a simple summation of elements
fn sum_fn<'a, T: Sum<&'a T>>(chunk: &'a [T]) -> T {
    chunk.into_iter().sum::<T>()
}

// summmation of squares of elements
fn sum_square_fn<'a, T>(chunk: &'a [T]) -> T
where
    T: Sum<&'a T> + Mul<Output = T> + Add<Output = T> + Default + Copy,
{
    chunk.into_iter().fold(T::default(), |sum, &x| sum + x * x)
}

// product of elements
fn prod_fn<'a, T: Product<&'a T>>(chunk: &'a [T]) -> T {
    chunk.into_iter().product::<T>()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在很容易在向量
上使用 *parallel_task* 方法

```
// first 20 integers
let vec: Vec<u64> = (1..=20).collect();

// parallel summation of integers
let mut v = vec.parallel_task(2, sum_fn);
println!("parallel_sum with 2 threads: {:?}", v);
assert_eq!(v.iter().sum::<u64>(), 210);

// parallel product of integer squares aka factorial
v = vec.parallel_task(4, prod_fn);
println!("parallel_product with 4 threads: {:?}", v);
assert_eq!(v.iter().product::<u64>(), 2432902008176640000);

// parallel sum of squares
v = vec.parallel_task(6, sum_square_fn);
println!("parallel_sum of squares with 6 threads: {:?}", v);
assert_eq!(v.iter().sum::<u64>(), 2870); 
```

Enter fullscreen mode Exit fullscreen mode

但是由于 fn 是一个泛型，我们可以使用任何类型。在下面，我用 *num* 格表示复数:

```
// parallel sum of complex squares
let complexes: Vec<Complex<u64>> = (1..=10).map(|i| Complex::new(i,i)).collect();
let mut v = complexes.parallel_task(6, sum_square_fn);
println!("parallel_sum of squares with 6 threads: {:?}", v);
assert_eq!(v.iter().sum::<Complex<u64>>(), Complex::new(0, 770)); 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我将尝试使用旨在简化并行迭代的 *rayon* crate，并使用其他类型。

> 照片由 Héctor J. Rivas 在 Unsplash 上拍摄