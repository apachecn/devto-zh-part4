# 生成器设计模式

> 原文：<https://dev.to/amrindersinghdev/builder-design-pattern-41d6>

**什么事？**
*Builder 是一种创造性的设计模式，用于使用循序渐进的方法从简单对象构建复杂对象*

**现实世界类比:**
*如果你曾经上过特斯拉的网站，你一定会看到他们有一步一步地选择汽车的方法，从基本模型开始，用户可以继续添加他们想要的功能。这可以在代码中通过伸缩一个构造函数并拥有所有可能的组合来处理，但是如果引入了一个新的特性呢？*

**解决方案**
*答案是设计模式生成器。构建器模式建议您将对象构造代码从它自己的类中提取出来，并将其移动到称为构建器的单独的
对象中。我们不用伸缩一个构造器来处理所有的情况，而是使用用户选择的特性来建造汽车。*

**代码示例:**

```
public class Director {
    BuilderInterface myBuilder;

    public void construct(BuilderInterface builder) {
        myBuilder = builder;
        myBuilder.buildBody();
        myBuilder.insertWheels();
        myBuilder.addHeadlights();
    }
}

public class Product {
    private LinkedList<String> parts;

    public Product () {
        parts = new LinkedList<String>();
    }

    public void add(String part) {
        parts.addLast(part);
    }

    public void show() {
        System.out.println("\n Product completed as below");

        for(int i = 0; i < parts.size(); i++)
            System.out.println(parts.get(i));
    }
}

public class Client {
    public static void main(String [] args) {
        System.out.println("***Builder Pattern Demo***\n");

        Director director = new Director();

        BuilderInterface carBuilder = new Car();
        BuilderInterface motorBuilder = new MotorCycle();

        // making a car
        director.construct(carBuilder);
        Product p1 = carBuilder.getVehicle();
        p1.show();

        // making a motorcycle
        director.construct(motorBuilder);
        Product p2 = motorBuilder.getVehicle();
        p2.show();
    }
}

interface BuilderInterface {
    void buildBody();
    void insertWheels();
    void addHeadlights();
    Product getVehicle();
}

class Car implements BuilderInterface {
    private Product product = new Product();

    @Override
    public void buildBody() {
        product.add("This is a body of a car");
    }

    @Override
    public void insertWheels() {
        product.add("4 wheels are added");
    }

    @Override
    public void addHeadlights() {
        product.add("2 headlights are added");
    }

    @Override
    public Product getVehicle() {
        return product;
    }
}

class MotorCycle implements BuilderInterface
{
    private Product product = new Product();

    @Override
    public void buildBody() {
        product.add("This is a body of a Motorcycle");
    }

    @Override
    public void insertWheels() {
        product.add("2 wheels are added");
    }

    @Override
    public void addHeadlights()  {
        product.add("1 Headlight has been added");
    }

    @Override
    public Product getVehicle() {
        return product;
    }
} 
```

**优势:**

*   它在一个物体的构造和表现之间提供了清晰的分离。
*   它对施工过程提供了更好的控制。
*   *支持改变对象的内部表示。*
*   *单一责任原则。*

**缺点**

*   代码的复杂性增加了。

**git hub Link:**
[Builder Design Pattern git hub repo](https://github.com/Amrindersingh1/Design-Patterns/tree/master/Creational/Builder)