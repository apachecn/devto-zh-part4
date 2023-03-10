# TIL:创建 Java 线程的两种方法

> 原文：<https://dev.to/bowlendev/til-two-ways-to-create-java-threads-4440>

[![A bundle of threads](img/8e1bb07ae012c2fdffd53935e6e9f5ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l0RwOO89--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/2800/1%2AXyDxK63szQtYNSaiIzj13w.jpeg)

Java 最强大的特性之一是它允许人们轻松地管理线程来执行程序中的各种任务。正确使用线程可以让你的程序非常高效。也就是说，今天我学到了创建这些线程的两种方法:

## 第一种方法:实现 Runnable

Runnable 是一个抽象可执行代码的接口。实现 Runnable 和它的方法 run()，我们就可以轻松地创建一个正常运行的线程:

```
class MyThread implements Runnable {
    Thread thread;

    MyThread() {
        thread = new Thread(this, "Sample Thread");
        thread.start();
    }
    public void run() {
        try {
            for(int i = 5; i > 0; i--) {
                System.out.println("child thread: " + i);
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            System.out.println("InterruptedException caught: Child Thread");
        }
        System.out.println("child thread exit");
    }
}

class MyThreadDemo {
    public static void main(String args[]) {
        new MyThread(); // Creates a new MyThread

        try {
            for(int i = 5; i > 0; i--) {
                System.out.println("main thread: " + i);
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            System.out.println("InterruptedException caught: Main Thread");
        }
        System.out.println("main thread exit");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要做这么简单的事情，这是相当多的代码，但是请记住，大部分代码是为了演示线程运行时发生的事情。

运行这段代码，输入如下:

```
main thread: 5
child thread: 5
child thread: 4
main thread: 4
child thread: 3
main thread: 3
main thread: 2
child thread: 2
main thread: 1
child thread: 1
main thread exit
child thread exit 
```

Enter fullscreen mode Exit fullscreen mode

您可能想知道为什么线程不保持它们的执行顺序，我会让您进一步了解线程，特别是关于序列化的内容。

实现 runnable，创建一个线程，然后启动那个线程，我们就可以运行了。真的就这么简单。

## 第二种方法:延长螺纹

我个人较少使用的是扩展一个预先存在的线程的做法。这主要是在我使用别人的技术，但没有权限编辑他们的源代码时使用的。
使用之前显示的例子，我可以做以下事情:

```
class MyThread extends Thread{
   // Collapsed for the sake of brevity
}
class ThreadDemo {
  public static void main(String args[]) {
    new MyThread(); // create a new thread

    try {
      for(int i = 5; i > 0; i--) {
        System.out.println("main thread: " + i);
        Thread.sleep(1000);
      }
    } catch (InterruptedException e) {
      System.out.println("InterruptedException caught - main");
    }
    System.out.println("main thread exit.");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出将类似:

```
main thread: 5
child thread: 5
child thread: 4
main thread: 4
child thread: 3
child thread: 2
main thread: 3
child thread: 1
child thread exit
main thread: 2
main thread: 1
main thread exit 
```

Enter fullscreen mode Exit fullscreen mode

同样，这种方法主要用于处理我实际上无法访问的代码。大多数情况下，实现 Runnable 是可行的。

祝你有美好的一天！