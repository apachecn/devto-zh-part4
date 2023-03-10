# Java 中的多线程

> 原文：<https://dev.to/vikashagrawal/multi-threading-in-java-il>

# Java 中的线程生命周期

一旦一个线程完成了它的执行，它将进入 Dead 状态，这意味着给定的线程不能再次启动，这意味着你不能调用 start 方法超过一次。join()方法等待一个线程死亡。换句话说，它会导致当前正在运行的线程停止执行，直到与之连接的线程完成其任务。

```
class TestJoinMethod1 extends Thread{  
 public void run(){   
  for(int i=1;i<=5;i++){  
   try{  
    Thread.sleep(500);  
   }catch(Exception e)
   {
    System.out.println(e);
   }  
  System.out.println(i);  
  }  
 }  

 public static void main(String args[]){  
  TestJoinMethod1 t1=new TestJoinMethod1();  
  TestJoinMethod1 t2=new TestJoinMethod1();  
  TestJoinMethod1 t3=new TestJoinMethod1();  
  t1.start();  
  try{   
   t1.join();  
  }catch(Exception e)
  {
   System.out.println(e);
  }   

 t2.start();  
 t3.start();  
}  
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:1 2 3 4 5 1 1 2 3 3 4 4 5 5

主线程将等待所有用户线程完成，然后退出。如果没有用户线程正在运行，那么守护线程也将死亡。

# 线程池

```
 public class TestThreadPool {  
     public static void main(String[] args) {  
        ExecutorService executor = Executors.newFixedThreadPool(5);//creating a pool of 5 threads 
        for (int i = 0; i < 10; i++) {  
            Runnable worker = new WorkerThread("" + i);  
            executor.execute(worker);//calling execute method of ExecutorService 
        }  
        executor.shutdown();  
        while (!executor.isTerminated()) {   }  

        System.out.println("Finished all threads");  
    }  
 } 
```

Enter fullscreen mode Exit fullscreen mode

输出

```
pool-1-thread-1 (Start) message = 0
pool-1-thread-2 (Start) message = 1
pool-1-thread-3 (Start) message = 2
pool-1-thread-5 (Start) message = 4
pool-1-thread-4 (Start) message = 3
pool-1-thread-2 (End)
pool-1-thread-2 (Start) message = 5
pool-1-thread-1 (End)
pool-1-thread-1 (Start) message = 6
pool-1-thread-3 (End)
pool-1-thread-3 (Start) message = 7
pool-1-thread-4 (End)
pool-1-thread-4 (Start) message = 8
pool-1-thread-5 (End)
pool-1-thread-5 (Start) message = 9
pool-1-thread-2 (End)
pool-1-thread-1 (End)
pool-1-thread-4 (End)
pool-1-thread-3 (End)
pool-1-thread-5 (End)
Finished all threads 
```

Enter fullscreen mode Exit fullscreen mode

# 等待-通知

```
package com.learnings.synch;

public class Tutorial {
   private static Account account = new Account();

   public static void main(String[] args) {
       Thread[] depositThreads = new Thread[5];
       Thread[] withdrawThreads = new Thread[8];
       for (int i = 0; i < 5; i++) {
           depositThreads[i] = new Thread(new UserThread(account, "deposit"));
       }
       for (int i = 0; i < 8; i++) {
           withdrawThreads[i] = new Thread(new UserThread(account, "withdraw"));
       }
       for (Thread thread : withdrawThreads) {
           thread.start();
       }
       try {
           Thread.sleep(5000);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
       for (Thread thread : depositThreads) {
           thread.start();
       }

   }
}

/**
*
*/
package com.learnings.synch;

/**
* @author Vikash Agrawal 
*
*/
public class Account {
   private int balance = 50;
   private Object lock = new Object();

   private void withdraw(int amount) {
       balance = balance - amount;
   }

   private void deposit(int amount) {
       balance = balance + amount;
   }

   public int getBalance() {
       return balance;
   }

   public void deposit() {
       synchronized (lock) {
           System.out.println("Will be deposited by: " + Thread.currentThread().getName());
           deposit(10);
           lock.notify();
           System.out.println("After deposit: " + getBalance());
       }
   }

   public void withdraw() {
       synchronized (lock) {
           System.out.println("Will be withdrawn by: " + Thread.currentThread().getName());
           if(getBalance() < 10) {
               try {
                   lock.wait();
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
           withdraw(10);
           System.out.println("After withdraw: " + getBalance());
       }
   }
}

/**
*
*/
package com.learnings.synch;

/**
* @author Vikash Agrawal 
*
*/
public class UserThread implements Runnable {
   private String operation;
   private Account account;

   public UserThread(Account account, String operation) {
       this.account = account;
       this.operation = operation;
   }

   public void run() {
       switch (operation) {
       case "deposit":
           account.deposit();
           break;
       case "withdraw":
           account.withdraw();
           break;
       }
   }
} 
```

Enter fullscreen mode Exit fullscreen mode