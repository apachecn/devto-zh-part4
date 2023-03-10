# 工厂设计模式

> 原文：<https://dev.to/amrindersinghdev/factory-design-pattern-3pah>

**什么事？**
*工厂方法是一种创造性的设计模式，它定义了用于创建对象的接口或抽象类，但允许子类决定将被实例化的对象的类型。*

问题
*假设你拥有一家出租车服务公司，向旅行者提供汽车作为交通工具。很快你的服务就出名了，你想扩展你的服务，提供其他的交通方式，比如自行车。为此，你必须改变你的整个代码库来支持自行车。*

**解决方案**
*工厂方法模式建议我们使用工厂方法为自己创建对象，而不是直接创建对象(使用 new 运算符)。在我们的例子中，我们没有创建一个新的汽车对象，而是让一个工厂方法为我们创建一个对象，并返回我们想要的汽车/自行车对象*

**类图:**

[![](img/a6c650a7655a8cc910a6b2915fc6e557.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GBPBVwMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xw0ufqymdwyieac91ok1.JPG)

**举例:**

```
class Factory {
    public static void main(String[] args) {

        Transport t1 = CabService.getInstance().getCab("CAR");
        System.out.println(t1.getRate());

        Transport t2 = CabService.getInstance().getCab("BIKE");
        System.out.println(t2.getRate());

    }
}

class CabService{
    private static CabService onlyInstance;

    // Make the constructor private so its only accessible to
    // members of the class.
    private CabService() {
    }

    // Create a static method for object creation
    synchronized public static CabService getInstance() {

        if (onlyInstance == null) {
            // Only instantiate the object when needed.
            synchronized (CabService.class) {
                if (onlyInstance == null) {
                    onlyInstance = new CabService();
                }
            }
        }
        return onlyInstance;
    }

    public Transport getCab(String type) {
        return new CabFactory().getTransport(type);
    }
}

class CabFactory {

    //Factory method that returns transport based on type requested
    public Transport getTransport(String type) {
        switch(type) {
            case "CAR" :
                return new Car();
            case "BIKE" :
                return new Bike();
            default:
                return null;
        }
    }
}

//Transport abstract class
abstract class Transport {
    double rate = 0;
    public double getRate() {
        return rate;
    }
}

class Car extends Transport {
    double rate = 10;
    public double getRate() {
        return rate;
    }
}

class Bike extends Transport  {
    double rate = 5;
    public double getRate() {
        return rate;
    }
} 
```

用途:

*   当一个类不知道需要创建什么子类时。
*   当一个类希望它的子类指定要创建的对象时。
*   当父类选择创建对象到它的子类时。
*   当您希望为库或框架的用户提供一种扩展其内部组件的方式时。
*   当您希望通过重用现有对象而不是每次都重新构建它们来节省系统资源时

**优点:**

*   工厂方法模式允许子类选择要创建的对象类型
*   它消除了将特定于应用程序的类绑定到代码中的需要，从而促进了松散耦合
*   单一责任原则
*   开/关原则

**缺点:**

*   代码可能会变得更加复杂，因为您需要引入许多新的子类来实现该模式。

**Github 回购链接:**
[工厂设计模式 Github](https://github.com/Amrindersingh1/Design-Patterns/tree/master/Creational/Factory)