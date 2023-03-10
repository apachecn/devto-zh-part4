# 把 RDD 分成几个小部分

> 原文：<https://dev.to/asifyar/divide-rdd-into-sub-parts-21g0>

**问题定义**

将火花 RDD 分成 N 个(由用户定义)子部分，并在这些子部分上执行相同的功能，每个子部分上有不同的数据集

**解释**

我要吃 RDD。我想把 RDD 分成子部分，就叫它分区吧。现在，在每一个分区上，我想执行相同的任务进行多次迭代。下面的代码很好地解释了

```
case class shape ( dim:Int) {
  val random = new Random()
  var X      : Array[Double]       =   Array.fill(dim)(random.nextDouble() * (100-10)+11 )
  var Y      : Array[Double]       =   Array.fill(dim)( math.random)
  var Loss       : Double              =   math.random
  var NewLoss       : Double              =   math.random 
}

val N = 1000 //in real N will be in millions
val d = 100 //in real N will be in millions
val nP = 4  // nP defines number of sub parts into which RDD is divided
val iterations = 1000 // in real analysis iteration will be in millions or billions  
val list = List.fill(N)(new shape(d))

 list.map { x =>
  x.Loss = function. SphereFunc(x.X) // Update Loss of each element
}

val rdd = sc.parallelize(batList, nP)
var partitioned = rdd.persist()

for(iter <- 1 to iterations) {

 partitioned = partitioned.mapPartitionsWithIndex { (k, iterator  )  =>
 val li = iterator.toList
 val localBest = li.minBy(_.Loss).X
 li.map { j =>
        j.Y = ((j.X, localBest).zipped.map(_ - _).map(_ * math.random), j.Y).zipped.map(_ + _)
        j.X = (j.X, j.Y).zipped.map(_ + _)

       }
 li.filter(math.random > _.Loss)
        .map { j =>
          j.X = localBest.map(_ + math.random)            
       }
 li.map{j => j.NewLoss = SphereFunc(j.X)

 li.filter(j => math.random < j.NewLoss && j.NewLoss < j.Loss).map { j =>
        j.Loss = j.NewLoss  
       }
  li.iterator
}

def SphereFunc(list: List[Double]): Double = {
list.foldLeft(0.0)((x, xs) => x + xs * xs)
} 
```

在这段代码中，我创建了 RDD，对于每次迭代，我都调用**mappartitionswindex**来获取每个分区的数据和每个分区的更新元素。

这段代码的问题是，对于每次迭代，当我调用**mappartitionswindex**时，它将对分区的每个元素执行所需的操作，并再次创建 RDD。因此分区不会保持不变。对于每次迭代，RDD 被再次划分为分区。也会引起很多混乱。

但是我想在开始时从 RDD 创建分区，然后在分区上执行所需的操作，直到用户定义的迭代完成。这里我调用了**mappartitionwithindex**迭代次数。是否可以只调用一次**mappartitionswindex**，然后遍历分区直到**次迭代**

我怎样才能做到这一点？我必须在超过 100 个内核的集群上运行实验。