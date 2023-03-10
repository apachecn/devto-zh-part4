# 如何创建 FrozenLake 随机地图

> 原文：<https://dev.to/rodolfomendes/how-to-create-frozenlake-random-maps-31a>

除了使用 desc 参数提供我们的自定义地图，还可以为冰冻的湖泊环境创建随机地图，如下面的代码所示:

```
# frozen-lake-random-maps-ex1.py
import gym

env = gym.make("FrozenLake-v0", desc=None, map_name=None)
env.reset()
env.render()
```

通过将参数 **desc** 和**地图名称**设置为**无**，将使用随机的 8×8 地图创建环境。然而，仅仅使用 **make** 方法是不可能创建自定义大小的随机地图的。但是我们可以通过从 **frozen_lake** 模块导入 **generate_random_map()** 函数并将其输出传递给 **desc** 参数来解决这个限制:

```
# frozen-lake-random-maps-ex2.py
import gym
from gym.envs.toy_text.frozen_lake
    import generate_random_map

random_map = generate_random_map(size=20, p=0.8)

env = gym.make("FrozenLake-v0", desc=random_map)
env.reset()
env.render()
```

**generate_random_map** ()函数为我们提供了更多的灵活性。该函数接收两个参数:**大小**和 **p** 。参数 **size** 需要一个表示网格边长的整数，从而得到一个 size x size 的贴图，而 **p** 参数是出现冻结图块的概率，用于设置冻结图块和孔洞之间的比例。

## 结论

健身房图书馆提供的 FrozenLake 环境具有有限的地图选项，但我们可以通过组合 **generate_random_map()** 函数和 **desc** 参数来解决这些限制。随机映射的使用测试我们的算法的泛化能力是很有趣的。

## 参考

例子:
[https://github . com/rodmsmendes/reinforcement learning 4 fun/tree/master/frozen-lake-random-maps](https://github.com/rodmsmendes/reinforcementlearning4fun/tree/master/frozen-lake-random-maps)

冰封湖游戏简介:
[https://reinforcement learning 4 . fun/2019/06/09/简介-强化-学习-冰封湖-实例/](https://reinforcementlearning4.fun/2019/06/09/introduction-reinforcement-learning-frozen-lake-example/)

使用冰封湖环境:
[https://reinforcement learning 4 . fun/2019/06/16/gym-tutorial-Frozen-Lake/](https://reinforcementlearning4.fun/2019/06/16/gym-tutorial-frozen-lake/)

安装 OpenAI 健身房:
[https://reinforcement learning 4 . fun/2019/05/24/how-to-install-open ai-Gym/](https://reinforcementlearning4.fun/2019/05/24/how-to-install-openai-gym/)

OpenAI 健身房项目主页:
[https://gym.openai.co](https://gym.openai.com/)T3】m