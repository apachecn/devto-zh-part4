# Rust 中的遗传算法

> 原文：<https://dev.to/d3spis3d/genetic-algorithm-in-rust-3gg>

最近我一直在学习 Rust，并尝试将其编译成 WebAssembly。在这一系列的三篇文章中，我将讲述我在 Rust 中构建一个遗传算法来解决旅行推销员问题的旅程，然后将它编译成 WebAssembly，以便在浏览器中运行该算法。我对 Rust 还很陌生，所以欢迎在评论或提供的回复中提出任何反馈。

这篇文章的所有代码都可以在这里找到。

[![A Genetic Algorithm is an optimisation algorithm inspired by the process of natural selection](img/43aebe5dcef038372e477db4ebde3ce6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GV5vbLmy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/88of5m9gesjd8c6rb9bb.jpg) 
照片由[阿德里安匡威](https://unsplash.com/@lurm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/science?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

遗传算法是一种受自然选择过程启发的优化算法。它在搜索空间中使用一群可能的解决方案；种群中最适合的个体生存下来，繁殖和变异的过程意味着更好的解决方案会在连续几代中进化。关于遗传算法更深入的解释，请参见本文。

我选择使用遗传算法的应用是旅行推销员问题，它可以总结为给定一个城市列表，访问所有城市的最短路径是什么？生成所有可能性并快速评估每个可能性的强力方法变得不切实际，因为排列的数量是城市数量的阶乘。例如:

*   5 个城市:5 个！= 120 种可能的解决方案
*   10 个城市:10 个！= 3 628 800 种可能的解决方案
*   20 个城市:20 个！=约 2 万亿分之一(约 2.43 x 10^18)

诸如遗传算法之类的优化算法可以用于搜索最优解，但是与强力或替代的精确算法不同，不能保证得到最优解。

第一步是决定如何构建我们问题的解决方案。这个问题的解决方案是要访问的节点的有序列表。 **Path** struct 存储经过的节点的有序列表，以及在创建个体时将计算的解决方案的适合度。为**路径**实现的方法允许个体繁殖和变异，并计算解决方案的适合度。

```
pub struct Path {
    fitness: f64,
    order: Vec<usize>
} 
```

使用节点的顺序列表和存储每个城市坐标的城市列表来计算解决方案的适合度。路径的开销计算为列表中城市之间的笛卡尔距离之和。该算法的目的是最大化适应度，在这种情况下是成本的倒数。

```
pub struct City {
    x: f64,
    y: f64,
}

impl Path {
    pub fn calculate_fitness(path: &Vec<usize>, city_list: &Vec<City>) -> f64 {
        let path_length = city_list.len();
        let mut cost = 0.0;
        for i in 0..path_length - 1 {
            let a = &city_list[path[i]];
            let b = &city_list[path[i + 1]];
            cost = cost + ((a.x - b.x).powf(2.0) + (a.y - b.y).powf(2.0)).sqrt();
        }

        1.0 / cost
    }
} 
```

一个个体将与群体中的另一个成员繁殖，并产生后代。使用来自[和](https://crates.io/crates/rand)箱的均匀概率分布来选择交叉点，从列表的开始直到交叉点选择来自母节点的节点，剩余的从父节点选择。

```
impl Path {
    pub fn breed(&self, other: &Path, city_list: &Vec<City>) -> Path {
        let order = Path::crossover_order(&self.order, &other.order);
        let fitness = Path::calculate_fitness(&order, city_list);

        Path { fitness, order }
    }

    fn crossover_order(mother: &Vec<usize>, father: &Vec<usize>) -> Vec<usize> {
        let mut rng = thread_rng();
        let crossover_point = Uniform::new(0, mother.len()).sample(&mut rng);

        let mother_dna = &mother[0..crossover_point];
        let mut father_dna: Vec<usize> = father.iter().filter_map(|d| {
            if !mother_dna.contains(d) {
                return Some(*d)
            }
            None
        }).collect();

        let mut child = Vec::new();
        child.extend_from_slice(mother_dna);
        child.append(&mut father_dna);

        child
    }
} 
```

每一代人口中的一些将经历突变，这涉及随机选择路径中的两个节点并交换它们。变异后，**路径**的适应度被重新计算。

```
pub fn mutate(&mut self, city_list: &Vec<City>) {
      let mut rng = thread_rng();
      let point_one = Uniform::new(0, self.order.len()).sample(&mut rng);
      let point_two = Uniform::new(0, self.order.len()).sample(&mut rng);

      self.order.swap(point_one, point_two);
      self.fitness = Path::calculate_fitness(&self.order, &city_list);
  } 
```

**模拟**结构用于协调连续世代的创建和最佳解决方案的选择。它存储种群以及遗传算法的参数，包括:

*   期望的最大迭代次数，
*   杂交率，用于繁殖的人口比例，
*   变异率，每个个体在每次迭代中变异的几率，
*   存活率，即繁殖群体中能延续到下一代的部分。

```
 pub struct Simulation {
     population: Vec<Path>,
     city_list: Vec<City>,
     max_iterations: usize,
     crossover_rate: f64,
     mutation_rate: f64,
     survival_rate: f64,
 } 
```

**模拟**有一个运行方法，它创建下一代，并为每次迭代找到新的最适合的个体。生成下一代包括:

*   从最好的个体中选择繁殖群体，
*   产生后代，
*   从繁殖群体中选择存活的亲本，
*   选择一些弱小的个体来帮助遗传多样性，
*   群体中的变异个体。

```
pub fn run(&mut self) -> () {
      let mut fittest = self.find_fittest();
      println!("starting iterations");

      for _ in 0..self.max_iterations {
          self.generate_next_generation();

          let challenger = self.find_fittest();

          if challenger.fitness > fittest.fitness {
              fittest = challenger;
          }
      }
      println!("{}", fittest);
  } 
```

完整的代码可以在[这里](https://github.com/d3spis3d/genetic-rust)找到。

在这篇文章中，我概述了一种遗传算法的方法。在本系列的下一篇文章中，我将利用这里打下的基础，并使用 WebAssembly 将其构建到一个 web 应用程序中。