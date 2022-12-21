# C#界面介绍

> 原文：<https://dev.to/gamersuji/c-intro-to-interface-3b3f>

# 前奏

你要知道，我不是一个了解界面的每个角落的界面专家。我将解释一个人如何有效地使用一个界面，并使他们的日常工作/代码变得不那么繁琐和整洁。此外，示例中的一些元素会有一些 unity 关键字，但这不会妨碍您学习我试图解释的基本概念。事不宜迟，我们进入正题。

# 什么是接口？

接口是一个基类，有一堆没有定义的方法声明，这个接口类不能独立工作，因为它的所有方法都是声明。它会期望其他类继承这个接口，并且在继承接口时，会强制被继承的类定义它内部的所有方法声明。本质上，一个接口告诉一个类它需要哪些方法来执行某些功能，但是接口不会告诉这个函数做什么，它希望这个类的作者知道它。我希望这一点很清楚，让我们继续一个例子，一个人将执行吃的功能，没有它一个人会死。如果人类是一个类，我们用接口 **IEat** 来连接人类这个类(*人总是在**接口**之前加上一个**“I”**所以让我们遵循相同的规则，这是不必要的，但我建议每个人都遵循相同的*)。这个 **IEat** 只会给类 **Human** 添加一个名为 **Eat** 的方法，这个方法会有一个名为**complete amount**的参数。记住这个方法或者接口的参数没有告诉我们怎么吃或者吃多少。他们只会在课堂上讲述这些事情，然后该类的作者应该指定如何吃食物以及吃多少/吃什么食物。

#### 这是上述功能的代码的样子

**IEat.cs** 界面

```
public interface IEat {

    void Eat(int consumateamount);
} 
```

**human . cs**T2】

```
public class Human : IEat
{
    public void Eat(int consumateamount)
    {
        PutItInMouth(GetFood(consumateamount));
    }

    private Food GetFood(int consumateamount)
    {
        Food food = new Food();
        food.apple = consumateamount;
        food.meat = consumateamount;
        return food;
    }

    public void PutItInMouth(Food food)
    {
        //Human Eats
    }
 }

public class Food
{
    public int apple;
    public int meat;
} 
```

### 现在让我们看看如何将这个**Eat(int consume amount)**用于人类测试对象。

让我们考虑人脑有一个名为 **BrainSchedule 的类，**在这个类中，我们将一天中的每个时间周期作为一个方法，在每个方法中，我们为一个人类主体的实例调用了**Eat(int consume amount)**方法。这里消耗的食物量是由时间周期法决定的。

```
public class BrainSchedule : MonoBehaviour
{
    private Human myHuman;

    private void Morning()
    {
        myHuman.Eat(2);
    }
    private void Noon()
    {
        myHuman.Eat(4);
    }
    private void Evening()
    {
        myHuman.Eat(1);
    }
    private void Night()
    {
        myHuman.Eat(2);
    }
} 
```

正如你所看到的， **BrainSchedule** 类假设 **Human** 类内部有一个 eat 方法，并在一天的不同周期调用该方法以保持人类营养良好。

### 我们刚刚学习了什么是**接口**，但这并不是它的最佳应用。

# 因此我们在这里有一个假想的场景，其中**接口**可以得到最好的利用。

假设我们在维护一个动物园，这个动物园里有各种各样的动物。每个动物都有自己的基类。作为动物园管理员，我们的主要工作是喂养它们。尽管每种动物都有自己独特的食物偏好，但我们可以根据它们的饮食习惯将它们分为三类。**食草动物、食肉动物和杂食动物**

**Writing the feeding system**

我们将有一个被称为喂养系统的公共类，它分配食物的数量，并根据类别给相应的动物喂食。这里需要注意的重要一点是，每个类别都有不同的子类别。例如，食草动物吃从干草到水果的各种食物。有些动物，如**考拉**会吃唯一的**桉树**。根据我们的设计，喂食系统只会根据主要类别隔离食物，如果树袋熊不想吃浆果而只吃桉树，那么树袋熊可以忽略浆果而吃桉树，而不会被喂食系统用勺子喂食。(在现实生活中，这将是一个非常不负责任的喂食系统，但为了学习界面，我们将忍受它)。

我们来定义一下这些类别会有哪些种类的食物

```
[System.Serializable] //Just so I could view this over the inspector
public class HerbivoreFood
{
    public int hay;
    public int strawberry;
    public int eucalyptus;
}

[System.Serializable]
public class CarnivoreFood
{
    public int salmon;
    public int steak;
    public int lamb;
}

[System.Serializable]
public class OmnivoreFood
{
    public CarnivoreFood carnivoreFood;
    public HerbivoreFood herbivoreFood;
} 
```

让我们基于三个不同的类别创建三个接口，每个基于类别的接口都将强加一个方法，该方法接受其类别类型的食物类型。**例如**，Iherbivore 接口应该有一个 **EatGreens** 方法，该方法以**食草动物**实例作为参数。下面提供了代码示例

```
public interface IHerbivore
{

    void EatGreens(HerbivoreFood herbivoreFood);

} 
```

```
public interface ICarnivore
{
    void EatMeat(CarnivoreFood carnivoreFood);
} 
```

```
public interface IOmnivore
{
    void EatGreensAndMeat(OmnivoreFood omnivoreFood);
} 
```

让我们为动物园中的每种动物创建一个基类，我们将让每种动物继承一个适合它们饮食习惯的接口。**例如，**如果一只动物喜欢**浆果**，那么只有当喂食系统识别出这只动物是**食草动物**并给它发送以食草动物为基础的食物时，它才能接触到那些浆果。对于喂养系统来说，识别一个给定的动物是否是食草动物，我们需要从 IHerbivore 接口继承一个动物的基类。(如果动物只想要吃肉，那么它应该从 **ICarnivore** 接口继承，否则如果它想要两者，那么它应该从 **IOmnivore** 接口继承。)下面给出了基类的代码。

```
public class Koala : MonoBehaviour, IHerbivore
{
    private int stomach;

    public void EatGreens(HerbivoreFood herbivoreFood)
    {
        EatEucalyptus(herbivoreFood.eucalyptus);
    }

    private void EatEucalyptus(int eucalyptus)
    {
        stomach += eucalyptus;
    }
} 
```

如前所述，由于**考拉从 **IHerbivore** 类继承了**类，它将与所有的食草动物食物一起发送，但是如果你仔细观察这个类，你会发现在收到所有的食草动物食物后，它只吃它想吃的东西，也就是**桉树**，剩下的就留下了。

```
public class Panther : MonoBehaviour, ICarnivore
{
    private int stomach;

    public void EatMeat(CarnivoreFood carnivoreFood)
    {
        EatSteak(carnivoreFood.steak);
    }

    private void EatSteak(int steak)
    {
        stomach += steak; 
    }
} 
```

同样，黑豹只吃**牛排**，即使它可以吃**肉食**类中的**羊肉**。

```
public class Bear : MonoBehaviour,IOmnivore
{
    private int stomach;

    public void EatGreensAndMeat(OmnivoreFood omnivoreFood)
    {
        EatBerries(omnivoreFood.herbivoreFood.strawberry);
        EatFish(omnivoreFood.carnivoreFood.salmon);

    }

    private void EatBerries(int berries)
    {
        stomach += berries;
    }

    private void EatFish(int salmon)
    {
        stomach += salmon;

    }
} 
```

而**熊**像往常一样作为杂食动物吃**浆果**和**鲑鱼**。

### 现在让我们来写送料系统

喂食系统应该首先访问动物园中的所有动物，所以我们有所有动物在 **allZooAnimals** 数组中，这是一个**游戏对象**的类型。(它是一个 **Unity** 具体类型不太注意，就当是一个常用的参考类型)。到目前为止，我们已经制作了三个动物脚本，所以我们将在数组中有三个动物，每个动物**游戏对象**将有三个动物脚本中的一个附加到它们身上。现在我们有了所有的动物，我们为每种食物类型创建对象，并在 **Start()** 方法中初始化它。之后，我们调用 **FeedAllAnimals()** 方法，该方法将识别数组中每个**游戏对象**所附加的每个动物脚本的类型，并调用适当的方法，根据其类别传递适当的食物类型。

```
public class FeedingSystem : MonoBehaviour
{

    public GameObject[] allZooAnimals;

    [SerializeField]private HerbivoreFood herbivoreFood;
    [SerializeField]private CarnivoreFood carnivoreFood;
    [SerializeField]private OmnivoreFood omnivoreFood;

    private void Start()
    {
        ///Get food ready
        herbivoreFood = new HerbivoreFood();
        herbivoreFood.hay = 50;
        herbivoreFood.eucalyptus = 50;
        herbivoreFood.strawberry = 100;

        carnivoreFood = new CarnivoreFood();
        carnivoreFood.steak = 60;
        carnivoreFood.salmon = 100;
        carnivoreFood.lamb = 100;

       omnivoreFood = new OmnivoreFood();
        omnivoreFood.herbivoreFood = herbivoreFood;
        omnivoreFood.carnivoreFood = carnivoreFood;

        FeedAllAnimals();
    }

    private void FeedAllAnimals()
    {
        for (int i = 0; i < allZooAnimals.Length; i++)
        {

            IHerbivore herbivore = allZooAnimals[i].GetComponent<IHerbivore>();

            if (herbivore != null)
            {
                herbivore.EatGreens(herbivoreFood);
            }

            ICarnivore carnivore = allZooAnimals[i].GetComponent<ICarnivore>();

            if (carnivore != null)
            {
                carnivore.EatMeat(carnivoreFood);
            }

            IOmnivore omnivore = allZooAnimals[i].GetComponent<IOmnivore>();

            if (omnivore != null)
            {
                omnivore.EatGreensAndMeat(omnivoreFood);
            }
        }
    }
} 
```

**FeedAllAnimals()** 方法中的 **GetComponent** 将获取动物的实例作为接口的类型，我们这样做而不是动物的基类，因为基类可能有很多功能，feeding 类不需要知道，通过将实例作为接口访问，我们知道接口中的这些方法将被强制写入继承它的类中， 因此，我们可以放心地传递数据，我们也可以使用它来检查动物的特定实例吃什么样的食物，而无需了解动物的任何信息，只需通过接口就可以知道它的饮食习惯。

如果你要给动物园买一只新动物，不要担心！只需给动物添加一个基于它们饮食的界面，它们就可以准备好被喂食了，例如，**，**看这只**老虎**和**斑马**。

```
public class Tiger : MonoBehaviour, ICarnivore
{
    private int stomach;

    public void EatMeat(CarnivoreFood carnivoreFood)
    {
        EatLamb(carnivoreFood.lamb);
    }

    private void EatLamb(int lamb)
    {
        stomach += lamb;
    }
} 
```

```
public class Zebra : MonoBehaviour, IHerbivore
{

    private int stomach;

    public void EatGreens(HerbivoreFood herbivoreFood)
    {
        EatHay(herbivoreFood.hay);
        EatBerries(herbivoreFood.strawberry);
    }

    private void EatHay(int hay)
    {
        stomach += hay;
    }

    private void EatBerries(int berries)
    {
        stomach += berries;
    }

} 
```

这标志着本教程的结束，或者不管你叫它什么，对我来说，这是迄今为止我见过的最好的界面用法。也许界面并不像看上去那样简单，如果你知道是这样的话，让我们在下面的评论部分讨论一下。

*附:这是我有生以来写的第一篇博客，所以如果你看到我在写作和编程方面可以提高的地方，请一定告诉我，谢谢 XOXO*