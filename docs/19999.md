# Java Lambda 表达式简介

> 原文：<https://dev.to/m_aamir/a-brief-introduction-to-java-lambda-expressions-2p3f>

让我向您介绍一个编程场景，如果您用 Java 编程已经有一段时间了，您可能已经很熟悉了。
通常你需要覆盖一个**函数接口**来为事件监听器提供回调或者提供一个函数的自定义实现，让我们看看下面的例子作为复习，在这里我们将一个回调方法传递给**欢迎类**，它在 5 秒延迟后调用我们提供的回调方法*函数接口*T7 的一个例子

```
interface onGreetCallback{
   public void greet(String greetMsg);
 }

class Greeter{
   private onGreetCallback callback;
   Greeter(onGreetCallback callback){
    this.callback = callback;

    //call the provided callback after 5 seconds delay
    try{
     Thread.sleep(5000);
     this.callback.greet("Hello, Pardon me, I am 5 seconds late to greet you.");
    }catch(InterruptedException  e){
      e.printStackTrace();
    }

   }
 }

 class DEMO{
   public static void main(String args[]){
    Greeter greeter =  new Greeter(new onGreetCallback(){
       @Override
       public void greet(String greetMsg){
         System.out.println(greetMsg);
       }
    });
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码非常常见，您每天都会在 java 中遇到类似的代码。如您所见，编写传递给 **Greeter 类构造函数**的 override 方法需要一些时间。
从 Java 8 开始，增加了 Lambda 表达式来简化这部分代码，让它看起来更干净优雅，更不用说更短了。
让我们再次编写**主**函数，这次使用 Lambda 表达式。

```
 public static void main(String args[]){
    Greeter greeter =  new Greeter(msg -> System.out.println(msg));
 } 
```

Enter fullscreen mode Exit fullscreen mode

Lambda 表达式为我们节省了至少 4 行代码。在下一篇文章中，我将详细讨论 lambda 表达式的语法、在哪里使用，以及与旧方法的一些比较。所以事不宜迟，让我们深入 Java Lambda 表达式的本质细节。

在上面修改过的代码中，**msg->system . out . println(msg)**是 lambda 表达式代码，其中 **msg** 是输入参数，
T5】system . out . println(msg)是 lambda 表达式的主体，- >是将输入参数与 lambda 主体分开的部分。

上面的 lambda 表达式是以一种非常简洁的方式编写的，一个更加扩展的版本应该是这样的。

```
 Greeter greeter =  new Greeter((msg) -> {
          System.out.println(msg);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你只有一个输入参数，你可以跳过括号，但是如果你有多个输入参数，那么你必须使用括号，例如
让我们也传入一个**名称**参数到我们的例子中。

```
 Greeter greeter =  new Greeter((msg, name) -> {
     System.out.println("Hello " + name);      
     System.out.println(msg);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

只有当你有多行代码时，才需要花括号，这就是为什么我给你看的第一个 lambda 表达式非常简洁。

```
 public static void main(String args[]){
    Greeter greeter =  new Greeter(msg -> System.out.println(msg));
 } 
```

Enter fullscreen mode Exit fullscreen mode

如果只有一条语句，return 关键字也不是必需的。让我们看一个例子，在 lambda 主体中有不止一个语句，然后看我们如何用一行代码编写相同的 lambda 表达式来删除显式的 return 关键字。

```
 interface FilterInterface{
    boolean filter(int number);
}

//Class to call the user implementation of method, on given data
class Utils {
    private FilterInterface filterInterface;

    //User provided Transformation Method
    void setFilterMethod(FilterInterface filterMethod) {
     this.filterInterface = filterMethod;
    }
    //Transform method to apply user-provided Method on given data
    List transform(List data) {
        List filtered_data = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            //Test with user provided Filter method
            if (this.filterInterface.filter(data.get(i))) {
               filtered_data.add(data.get(i));
            }
        }
        return filtered_data;
    }
}
public class Main{
    public static void main(String[] args) {
        List data =  new ArrayList<>();
        data.add(1);
        data.add(2);
        data.add(3);
        data.add(4);
        data.add(5);
        data.add(6);

        Utils utils =  new Utils(); //Instantiate object of our custom util class
        //Let's provide a Lambda Expression to filter Even numbers
        /*
        * Because we have more than a single line of code, we have to use the return keyword
        * */
        utils.setFilterMethod(x -> {
            if (x % 2 ==0){
                return true;
            }
            return false;
        });

        //Apply the provided transformation function
        List filtered_data =  utils.transform(data);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我特意用这种长方式写了上面的 lambda 表达式，让我们用单行写，跳过 return 关键字。

```
 utils.setFilterMethod(x -> x % 2 == 0); 
```

Enter fullscreen mode Exit fullscreen mode

#### λ表达式到功能接口的映射

要将 lambda 映射到功能接口，必须满足一些条件。

1.  该接口必须至少包含一个抽象方法。
2.  Lambda 表达式的输入参数必须与抽象方法的输入参数相匹配。
3.  lambda 表达式的返回类型必须与抽象方法的返回类型匹配。

#### Lambda 表达式也允许你捕捉变量。

您可以捕获在 lambda 函数体外部声明的变量。以下变量可以在 lambda 的主体内部捕获。

*   局部变量。
*   实例变量。
*   静态变量。

##### 局部变量捕获

```
 class DEMO{
    final String name = "Aamir";
    public static void main(String args[]){
     Greeter greeter =  new Greeter((msg) -> {
       System.out.println("Hello " + name);      
       System.out.println(msg);
     });
   }
  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们在 lambda 主体中捕获了 **name** 变量。**被捕获的局部变量必须声明为 final** ，如果变量没有声明为 final，那么 java 编译器会报错。

##### 实例变量捕获

Lambda 表达式还允许您捕获**实例变量**，这里值得注意的一个重要区别是，这在您匿名实现接口的经典方法中是不可能的，**这是因为匿名接口有自己的主体，而 lambda 表达式没有**。

```
 class MyClass{
   private String name = "Aamir";
   Greeter greeter =  new Greeter((msg) -> {
     System.out.println("Hello " + this.name);      
     System.out.println(msg);
   });
 } 
```

Enter fullscreen mode Exit fullscreen mode

##### 静态变量捕捉

```
 class DEMO{
    private static String name = "Aamir";
    public static void main(String args[]){
     Greeter greeter =  new Greeter((msg) -> {
       System.out.println("Hello " + name);      
       System.out.println(msg);
     });
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经看到了 lambda 表达式是如何工作的，它们的结构和不同风格的语法，让我们来看一个来自 **Java Stream API** 的更实际的例子，它允许您以声明的方式编写代码。

##### 对数组或列表应用变换/映射函数

```
public class Main{
    public static void main(String[] args) {
        int fav_nums[] = {1,2,3,4,5};
        //Apply the array transformation Using a Lambda Expression
        int new_fav_nums[] = Arrays.stream(fav_nums).map(x -> x *2).toArray(); // 2,4,6,8,10
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 根据起始字符过滤名称

```
public class Main{
    public static void main(String[] args) {
        String names[] = {"Aamir" ,
                            "Ali" ,
                            "Atif" ,
                            "Siraj" ,
                            "Shehriyaar" ,
                            "Shehroz" ,
                            "Hassan" ,
                            "Jibran"};
        //Applying filter to only get names starting with "A"
        String filtered_names[] = Arrays.stream(names).filter(name -> name.startsWith("A")).toArray(String[]::new);
        //Aamir
       // Ali
      //  Atif
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上，我们可以通过抽象 lambda 表达式中的逻辑，并通过传入方法引用，在它自己的方法中取出它，从而使上面的代码更加紧凑。

```
 class MyFilters {
    public static boolean filterName(String name){
        return name.startsWith("A");
    }
}
public class Main{
    public static void main(String[] args) {
        String names[] = {"Aamir" ,
                            "Ali" ,
                            "Atif" ,
                            "Siraj" ,
                            "Shehriyaar" ,
                            "Shehroz" ,
                            "Hassan" ,
                            "Jibran"};
        //Applying filter to only get names starting with "A"
        //Using a method reference
        String filtered_names[] = Arrays.stream(names).filter(MyFilters::filterName).toArray(String[]::new);
        //Aamir
       // Ali
      //  Atif
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

过滤器将对数组中的每一项调用我们提供的方法引用。

对于那些想知道将 lambda 表达式逻辑抽象成一个单独的类有什么用的人，请允许我分享一些用例，如果你需要在不同的数组或列表集合上应用某种类似的过滤、映射、归约操作，这个过程将非常有用。也可能是你的 lambda 表达式体太乱了。这也使得你的代码对其他开发人员来说更具可读性，他们会通过你传入的方法引用的名称立即理解你的代码的含义，更容易理解你的思维过程。如果 6 个月后你决定做出一些改变，这也会阻止你变得完全疯狂。我知道我们都经历过。

*通知 java 编译器这是一个**方法引用**

您可以引用以下类型的方法:

*   静态方法。
*   参数对象上的实例方法。
*   实例方法。
*   构造函数。

希望到目前为止，您已经很好地理解了什么是 Java Lambda 表达式，如何编写它们，以及它们的用法。如果这是您第一次接触 lambda，您可能需要更多的时间和练习来完全掌握它，但是相信我，一旦您学习并掌握了它们，它们将是您处理数据集合时的首选工具。

> "永远关注你已经走了多远，而不是你还有多远要走."