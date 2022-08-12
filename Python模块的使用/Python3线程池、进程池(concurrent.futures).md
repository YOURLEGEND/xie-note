**目录**

[线程池进程池的使用](#t0)

[线程池设置为多少合适？](#t1)

* * *

在上一节中我们提到了多线程，传送门：[Python多线程\_thread和Threading](https://xie1997.blog.csdn.net/article/details/83656021)

**多线程的问题？**

传统多线程方案使用“即时创建， 即时销毁”的策略。尽管与创建进程相比，创建线程的时间已经大大的缩短，但是如果提交给线程的任务是执行时间较短，而且执行次数极其频繁，那么服务器将处于不停的创建线程，销毁线程的状态。

​ 一个线程的运行时间可以分为3部分：线程的启动时间、线程体的运行时间和线程的销毁时间。在多线程处理的情景中，如果线程不能被重用，就意味着每次创建都需要经过启动、销毁和运行3个过程。这必然会增加系统相应的时间，降低了效率。

有没有一种高效的解决方案呢？ —— **线程池**

**线程池基本原理**

我们把任务放进队列中去，然后开N个线程，每个线程都去队列中取一个任务，执行完了之后告诉系统说我执行完了，然后接着去队列中取下一个任务，直至队列中所有任务取空，退出线程。

**使用线程池的优势**

由于线程预先被创建并放入[线程池](https://so.csdn.net/so/search?q=%E7%BA%BF%E7%A8%8B%E6%B1%A0&spm=1001.2101.3001.7020)中，同时处理完当前任务之后并不销毁而是被安排处理下一个任务，因此能够避免多次创建线程，从而节省线程创建和销毁的开销，能带来更好的性能和系统稳定性。

Python3 线程池、进程池使用的是 concurrent.futures 包

### 线程池进程池的使用

使用时，需导入

```
#线程池      
from concurrent.futures import ThreadPoolExecutor      
#进程池      
from concurrent.futures import ProcessPoolExecutor
```


正确使用线程池、进程池，如下。

```
from concurrent.futures import ThreadPoolExecutor as TPool    #导入线程池      
#from concurrent.futures import ProcessPoolExecutor as PPool   #导入进程池       
def test(arg1,arg2,arg3):      
	pass       
tpool=TPool(100)  #定义线程池里面有100个线程      
#ppool=PPool(100)  #定义进程池里面有100个进程      
for a in range(50):      1
	for b in range(50):      1
		for c in range(50):      1
			tpool.submit(test,a,b,c)      1
			tpool.submit(test,a,b,c).result()  #获得函数返回的结果      1
			#ppool.submit(test,a,b,c)
```


### 线程池设置为多少合适？

服务器CPU核数有限，能够同时并发的线程数有限，并不是开得越多越好，以及线程切换是有开销的，如果线程切换过于频繁，反而会使性能降低

线程执行过程中，计算时间分为两部分：

*   CPU计算，占用CPU
*   不需要CPU计算，不占用CPU，等待IO返回，比如recv(), accept(), sleep()等操作，具体操作就是比如访问cache、RPC调用下游service、访问DB，等需要网络调用的操作

那么如果计算时间占50%， 等待时间50%，那么为了利用率达到最高，可以开2个线程。  
假如工作时间是2秒， CPU计算完1秒后，线程等待IO的时候需要1秒，此时CPU空闲了，这时就可以切换到另外一个线程，让CPU工作1秒后，线程等待IO需要1秒，此时CPU又可以切回去，第一个线程这时刚好完成了1秒的IO等待，可以让CPU继续工作，就这样循环的在两个线程之前切换操作。

那么如果计算时间占20%， 等待时间80%，那么为了利用率达到最高，可以开5个线程。  
可以想象成完成任务需要5秒，CPU占用1秒，等待时间4秒，CPU在线程等待时，可以同时再激活4个线程，这样就把CPU和IO等待时间，最大化的重叠起来

抽象一下，计算线程数设置的公式就是：   
N核服务器，通过执行业务的单线程分析出本地计算时间为x，等待时间为y，则工作线程数（线程池线程数）设置为 N\*(x+y)/x，能让CPU的利用率最大化。   
由于有GIL的影响，python只能使用到1个核，所以这里设置N=1

参考文章：[https://blog.csdn.net/daiyu\_\_zz/article/details/81912018](https://blog.csdn.net/daiyu__zz/article/details/81912018)