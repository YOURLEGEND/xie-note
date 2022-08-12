**目录**

[线程](#t0)

[一个线程的生命周期](#t1)

[线程的创建与启动](#t2)

[线程的终止](#t3)

[线程的同步机制](#t4)

[线程的通知机制](#t5)

* * *

线程
--

一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。

多线程是多任务的一种特别的形式，但多线程使用了更小的资源开销。

这里定义和线程相关的另一个术语 - 进程：一个进程包括由操作系统分配的内存空间，包含一个或多个线程。一个线程不能独立的存在，它必须是进程的一部分。一个进程一直运行，直到所有的非守护线程都结束运行后才能结束。

多线程能满足程序员编写高效率的程序来达到充分利用 CPU 的目的。

### 一个线程的生命周期

线程是一个动态执行的过程，它也有一个从产生到死亡的过程。

下图显示了一个线程完整的生命周期。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxNC8wMS9qYXZhLXRocmVhZC5qcGc?x-oss-process=image/format,png)

*   **新建状态:**
    
    使用 **new** 关键字和 **Thread** 类或其子类建立一个线程对象后，该线程对象就处于新建状态。它保持这个状态直到程序 **start()** 这个线程。
    
*   **就绪状态:**
    
    当线程对象调用了start()方法之后，该线程就进入就绪状态。就绪状态的线程处于就绪队列中，要等待JVM里线程调度器的调度。
    
*   **运行状态:**
    
    如果就绪状态的线程获取 CPU 资源，就可以执行 **run()**，此时线程便处于运行状态。处于运行状态的线程最为复杂，它可以变为阻塞状态、就绪状态和死亡状态。
    
*   **阻塞状态:**
    
    如果一个线程执行了sleep（睡眠）、suspend（挂起）等方法，失去所占用资源之后，该线程就从运行状态进入阻塞状态。在睡眠时间已到或获得设备资源后可以重新进入就绪状态。可以分为三种：
    
    *   等待阻塞：运行状态中的线程执行 wait() 方法，使线程进入到等待阻塞状态。
        
    *   同步阻塞：线程在获取 synchronized 同步锁失败(因为同步锁被其他线程占用)。
        
    *   其他阻塞：通过调用线程的 sleep() 或 join() 发出了 I/O 请求时，线程就会进入到阻塞状态。当sleep() 状态超时，join() 等待线程终止或超时，或者 I/O 处理完毕，线程重新转入就绪状态。
        
*   **死亡状态:**
    
    一个运行状态的线程完成任务或者其他终止条件发生时，该线程就切换到终止状态。
    

### 线程的创建与启动

Java 提供了三种创建线程的方法：

*   通过实现 Runnable 接口；
*   通过继承 Thread 类本身；
*   通过 Callable 和 Future 创建线程。

**方法一：实现Runnable接口**

这是因为 java 不允许多重继承，如果你的类已经继承于别的类，又要作为线程来运行，则可以使用这种方式

```
public class Hello implements  Runnable{      
       public void run(){      
               //填写要干的事      
   }      
}      
Hello obj=new Hello();      
Thread t=new Thread(obj);      
t.start();                             //线程的启动
```


**方法二： 继承 Thread 类**

```
public class Hello  extends Thread{      
        public void  run(){      
                 //填写要干的事      
     }      
}      
new Hello().start;               //线程的启动
```


**方法三：通过 Callable 和 Future 创建线程**

*   1\. 创建 Callable 接口的实现类，并实现 call() 方法，该 call() 方法将作为线程执行体，并且有返回值。
    
*   2\. 创建 Callable 实现类的实例，使用 FutureTask 类来包装 Callable 对象，该 FutureTask 对象封装了该 Callable 对象的 call() 方法的返回值。
    
*   3\. 使用 FutureTask 对象作为 Thread 对象的 target 创建并启动新线程。
    
*   4\. 调用 FutureTask 对象的 get() 方法来获得子线程执行结束后的返回值。
    

**创建线程的三种方式的对比：**

*   采用实现 Runnable、Callable 接口的方式创建多线程时，线程类只是实现了 Runnable 接口或 Callable 接口，还可以继承其他类。
    
*   使用继承 Thread 类的方式创建多线程时，编写简单，如果需要访问当前线程，则无需使用 Thread.currentThread() 方法，直接使用 this 即可获得当前线程。
    

### 线程的终止

当一个线程的start()调用后，线程为Alive状态。当一个线程的主函数run()退出后，线程死亡(Dead);想让一个线程终止，就是要想办法让它从run()中退出来。当一个线程在sleep的时候，必须使用t.interrupt()来中断目标线程才可以使线程退出。等待线程的退出t.join();

```
//线程的终止      
public class Test1 extends Thread{      
  public boolean quitflag=false;      
  public void run()      
  {      
    for(int i=0;i<10;i++){      
       if(quitflag){               
         break;      
       }      1
       System.out.println("---------");      1
       try {      1
           Thread.sleep(1000);      1
       } catch (InterruptedException e) {      1
           e.printStackTrace();      1
       }      1
     }      1
  }      1
}       2
public class Hello {      2
  public static void main(String[] args) {      2
    // TODO 自动生成的方法存根      2
    Test1 t1=new Test1();      2
    t1.start();      2
    //接受用户输入 输入后终止线程      2
    InputStreamReader m=new InputStreamReader(System.in);      2
    BufferedReader read=new BufferedReader(m);      2
    try {      2
       read.readLine();      3
       read.close();      3
       t1.quitflag=true;       //线程终止      3
       t1.interrupt();         //使正在sleep的线程终止      3
       t1.join();             //等待线程退出      3
    } catch (Exception e) {      3
       // TODO 自动生成的 catch 块      3
       e.printStackTrace();      3
    }      3
    System.out.println("结束了");      3
  }      4
}
```


### 线程的同步机制

当两个或多个线程同时访问一个对象时，可能发生数据不同步的现象。为了实现多线程对同一对象的同步访问，引入互斥锁的概念。

```
synchronized(lock)               //申请锁      
{               
            //关键代码      
}
```


*   若别的线程正持有该锁，则本线程阻塞等待
*   若此锁空闲，则本线程持有锁，进入大括号执行，完毕后释放锁

### 线程的通知机制

**wait/notify 通知机制** 

1.  调用 wait(),notify(),notifyAll()时需要先对调用对象加锁。
2.  调用wait()方法后，线程由RUNNING变为WAITING，并将当前线程放置于对象等待队列，释放锁
3.  notify(),notifyAll()方法被调用后，等待线程依然不会从wait()方法返回，而是等调用notify(),notifyAll()的线程释放该锁之后,等待线程才有机会从wait()返回。
4.  notify()方法将等待队列中的一个等待线程从等待队列中移到同步队列中，而 notifyAll()方法则是把所有等待线程从等待队列中移到同步队列中，被移动的线程的状态由WAITING变成BLOCKED
5.  从wait()方法返回的前提是获得了调用对象的锁。```
public class Test1 {      
  static boolean flag=true;      
  static Object lock=new Object();       
  public static void main(String[] args) {      
    One a=new One();      
    a.start();      
    Two b=new Two();      
    b.start();      1
  }       1
  static class One extends Thread{      1
    public void run(){      1
       synchronized(lock){      1
         while(flag){      1
           try {      1
             System.out.println("flag is true");      1
             lock.wait();       //线程等待，释放锁      1
           } catch (InterruptedException e) {      2
             e.printStackTrace();      2
           }      2
         }      2
         System.out.println("结束");      2
       }      2
     }      2
    }      2
  static class Two extends Thread{      2
    public void run(){      2
       synchronized (lock) {      3
         System.out.println("flag is false");      3
         flag=false;      3
         lock.notify();      3
         System.out.println("notify之后，不会立即退出！");      3
       }      3
    }      3
  }      3
}
```

    

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-9cd8a5a3c9544be3979c77426d717abd)[Java异步任务](https://edu.csdn.net/skill/java/java-9cd8a5a3c9544be3979c77426d717abd)[线程与进程](https://edu.csdn.net/skill/java/java-9cd8a5a3c9544be3979c77426d717abd)44190 人正在系统学习中