**目录**

[多线程](#t0)

[\_thread模块(不常用)](#t1)

[使用 \_thread模块创建多线程](#t2)

[threading](#t3)

[使用 threading模块创建多线程](#t4)

[线程同步(Lock)](#t5)

[线程优先级队列(Queue)](#t6)

* * *

在讲多线程之前，我们先看一个单线程的例子：

```
import _thread      
import time      
from datetime import datetime       
def Test(name):      
    for i in range(3):      
        print(name,datetime.now())      
        time.sleep(1)      
def main():      1
    Test("one")      1
    Test("two")       1
if __name__=='__main__':      1
    start=time.time()      1
    main()      1
    end=time.time()      1
    print("运行程序花费了%s秒"%(end-start))      1
##################################################      1
one 2018-11-11 11:17:57.984571      2
one 2018-11-11 11:17:58.987543      2
one 2018-11-11 11:17:59.988000      2
two 2018-11-11 11:18:00.988495      2
two 2018-11-11 11:18:01.989449      2
two 2018-11-11 11:18:02.990809      2
运行程序花费了6.008334159851074秒
```


可以看到，对于单线程，运行完这段程序需要6秒的时间。

现在我们开始讲[多线程](https://so.csdn.net/so/search?q=%E5%A4%9A%E7%BA%BF%E7%A8%8B&spm=1001.2101.3001.7020)了！

Python3通过两个标准库 \_thread 和 threading 提供对线程的支持。

\_thread 提供了低级别的、原始的线程以及一个简单的锁，python3中已经弃用了\_thread库。而threading是一种更高层次封装的线程库，threading 模块除了包含 \_thread 模块中的所有方法外，还提供其他方法。所以我们一般常用的是 threading 库。

多线程
===

多线程类似于同时执行多个不同程序，多线程运行有如下优点：

*   使用线程可以把占据长时间的程序中的任务放到后台去处理。
*   用户界面可以更加吸引人，这样比如用户点击了一个按钮去触发某些事件的处理，可以弹出一个进度条来显示处理的进度
*   程序的运行速度加快
*   在一些等待的任务实现上，如用户输入、文件读写和网络收发数据等，线程就比较有用了。在这种情况下我们可以释放一些珍贵的资源如内存占用等等。

线程在执行过程中与进程还是有区别的。每个独立的进程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，线程必须依存在进程中，由应用程序提供多个线程执行控制。

一个进程只能运行在一个核中。所以如果多线程运行，这些线程都是在一个核中运行。

每个线程都有他自己的一组CPU寄存器，称为线程的上下文，该上下文反映了进程上次运行该线程的CPU寄存器的状态。

*   线程可以被抢占（中断）。
*   在其他线程正在运行时，线程可以暂时搁置（也称为睡眠） -- 这就是线程的退让

Python中使用线程有两种方式：函数 或者用 类 来包装线程对象。\_thread 模块用的是函数式线程，threading 可以包装线程对象，也可以使用函数式线程

\_thread模块(不常用)
---------------

函数式：调用 \_thread 模块中的 start\_new\_thread() 函数来产生新线程。语法如下:

> \_thread.start\_new\_thread ( function, args \[, kwargs\] )

参数说明:

*   function - 线程函数
*   args - 传递给线程函数的参数,他必须是个 tuple 类型
*   kwargs - 可选参数

### 使用 \_thread模块创建多线程

对于上面这个例子，我们用多线程来实现，看需要多长的时间

```
import _thread      
import time      
from datetime import datetime       
def Test(name):      
    for i in range(3):      
        print(name,datetime.now())      
        time.sleep(1)      
def main():      1
        _thread.start_new_thread(Test,("one ",))      1
        _thread.start_new_thread(Test,("two ",))       1
if __name__=='__main__':      1
    start=time.time()      1
    main()      1
    end=time.time()      1
    print("运行程序花费了%s秒"%(end-start))      1
############################################################      1
运行程序花费了0.0秒two  2018-11-11 11:30:53.353519      2
one       2
 2018-11-11 11:30:53.360632      2
two  2018-11-11 11:30:54.361750      2
one  2018-11-11 11:30:54.368121      2
two  2018-11-11 11:30:55.364558      2
one  2018-11-11 11:30:55.369716
```


咦，为什么程序花费0.0秒呢？而且是第一个打印呢？原因在于当子线程还在sleep的时候，我们的父线程就已经执行完了。父线程并没有等待让子线程执行完再执行。所以最后打印程序花费的时间是0.0秒。但是看子线程打印出来的东西我们可以看到，运行程序是花费了3秒时间的，比单线程省了一半的时间。

这里父线程并没有等待说子线程执行完再执行，这里就涉及到了一个阻塞问题。阻塞问题我们在 threading模块中讲解，利用 threading模块中的 join() 方法来阻塞。 

threading
---------

threading 模块除了包含 \_thread 模块中的所有方法外，还提供其他方法：

*   **threading.currentThread()：** 返回当前的线程变量。
*   **threading.enumerate()：**  返回一个包含正在运行的线程的list列表。正在运行指线程启动后、结束前。
*   **threading.activeCount()：** 返回正在运行的线程数量，与 len(threading.enumerate()) 有相同的结果。

除了使用方法外，threading 线程模块同样提供了 threading.Thread 类来处理线程，threading.Thread类提供了以下方法:

*   **run():** 用以表示线程活动的方法。
*   **start():**启动线程活动。
*   **join(\[time\]):** 父线程等待子线程至中止再执行。这阻塞调用线程直至线程的join() 方法被调用中止、正常退出或者抛出未处理的异常、再或者是可选的超时发生。
*   **isAlive():** 返回线程是否活动的。
*   **getName():**  返回线程名。
*   **setName():**  设置线程名。

### 使用 threading模块创建多线程

我们可以通过创建一个子类继承于 threading.Thread ，并实例化后调用 start() 方法启动新线程，即它调用了线程的 run() 方法：

我们这里使用了 join() 方法来阻塞父线程，让他等待子线程运行完再运行，可以看到，最后我们花费了３秒的时间，是单线程的一半。

```
import threading      
import time      
from datetime import datetime       
class myThread(threading.Thread):      
    def __init__(self,name):      
        threading.Thread.__init__(self)      
        self.name=name      
    def run(self):      1
        for i in range(3):      1
            print(self.name,datetime.now())      1
            time.sleep(1)      1
def main():      1
    thread1=myThread("one ")      1
    thread2=myThread("two ")      1
    thread1.start()           #启动线程，代用了该线程的 run() 方法      1
    thread2.start()      1
    thread1.join()       #必须等子线程运行完了父线程才可以运行      1
    thread2.join()         2
if __name__=='__main__':      2
    start=time.time()      2
    main()      2
    end=time.time()      2
    print("运行程序花费了%s秒"%(end-start))      2
###########################################      2
one  2018-11-11 16:14:50.546845      2
two  2018-11-11 16:14:50.554877      2
one  2018-11-11 16:14:51.555091      2
two  2018-11-11 16:14:51.563160      3
one  2018-11-11 16:14:52.556510      3
two  2018-11-11 16:14:52.564490      3
运行程序花费了3.0182247161865234秒
```


**我们也可以使用函数来创建多线程** 

```
import threading      
import time      
from datetime import datetime       
def Test(name):      
    for i in range(3):      
        print(name,datetime.now())      
        time.sleep(1)      
def main():      1
        t1=threading.Thread(target=Test,args=("one",))  #调用threading.Thread函数，target参数是要执行的函数，args是要传入的参数，为元组类型      1
        t2=threading.Thread(target=Test,args=("two",))      1
        t1.start()        #启动线程      1
        t2.start()      1
        t1.join()       #必须等子线程运行完了父进程才可以运行      1
        t2.join()      1
if __name__=='__main__':      1
    start=time.time()      1
    main()      1
    end=time.time()      2
    print("运行程序花费了%s秒"%(end-start))      2
##########################################################      2
one 2018-11-11 16:18:54.727638      2
two 2018-11-11 16:18:54.735688      2
one 2018-11-11 16:18:55.736727      2
two 2018-11-11 16:18:55.743748      2
one 2018-11-11 16:18:56.740741      2
two 2018-11-11 16:18:56.746846      2
运行程序花费了3.0212459564208984秒
```


![](https://img-blog.csdnimg.cn/20200119100757683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

线程同步(Lock)
----------

如果多个线程共同对某个数据修改，则可能出现不可预料的结果，为了保证数据的正确性，需要对多个线程进行同步。

使用 threading 对象的 Lock 和 Rlock 可以实现简单的[线程同步](https://so.csdn.net/so/search?q=%E7%BA%BF%E7%A8%8B%E5%90%8C%E6%AD%A5&spm=1001.2101.3001.7020)，这两个对象都有 acquire() 方法和 release() 方法，对于那些需要每次只允许一个线程操作的数据，可以将其操作放到 acquire() 和 release() 方法之间。

多线程的优势在于可以同时运行多个任务（至少感觉起来是这样）。但是当线程需要共享数据时，可能存在数据不同步的问题。

考虑这样一种情况：

一个列表里所有元素都是0，线程"set"从后向前把所有元素改成1，而线程"print"负责从前往后读取列表并打印。

那么，可能线程"set"开始改的时候，线程"print"便来打印列表了，输出就成了一半0一半1，这就是数据的不同步。为了避免这种情况，引入了锁的概念。

锁有两种状态——锁定和未锁定。每当一个线程比如"set"要访问共享数据时，必须先获得锁定；如果已经有别的线程比如"print"获得锁定了，那么就让线程"set"暂停，也就是同步阻塞；等到线程"print"访问完毕，释放锁以后，再让线程"set"继续。

经过这样的处理，打印列表时要么全部输出0，要么全部输出1，不会再出现一半0一半1的尴尬场面。

实例：

```
import threading      
import time      
from datetime import datetime      
from threading import Lock      
lock=Lock()  #得到一个Lock对象       
#方法一      
def Test(name):      
    lock.acquire()       ##获取锁，用于线程同步      1
    for i in range(3):      1
        print(name,datetime.now())      1
        time.sleep(1)      1
    lock.release()      #释放锁，开启下一个线程       1
#方法二      1
def Test(name):      1
    with lock:      1
        for i in range(3):      1
            print(name,datetime.now())      2
            time.sleep(1)       2
def main():      2
        t1=threading.Thread(target=Test,args=("one",))  #调用threading.Thread函数，target参数是要执行的韩三户，args是要传入的参数，为元组类型      2
        t2=threading.Thread(target=Test,args=("two",))      2
        t1.start()        #启动线程      2
        t2.start()      2
        t1.join()       #必须等子线程运行完了父线程才可以运行      2
        t2.join()      2
if __name__=='__main__':      3
    start=time.time()      3
    main()      3
    end=time.time()      3
    print("运行程序花费了%s秒"%(end-start))      3
##################################################################      3
one 2018-11-11 16:21:42.749223      3
one 2018-11-11 16:21:43.757153      3
one 2018-11-11 16:21:44.761436      3
two 2018-11-11 16:21:45.764834      3
two 2018-11-11 16:21:46.788177      4
two 2018-11-11 16:21:47.791732      4
运行程序花费了6.047051906585693秒
```


从上面代码可以看出，对函数进行了线程锁定。这样当我们的线程1和线程2都同时调用该函数时， 必须等线程1执行完了线程2再执行，这样，程序花费的时间就相当于单线程时候的6秒了。

线程优先级队列(Queue)
--------------

Python 的 Queue 模块中提供了同步的、线程安全的队列类，包括：

*   FIFO(先进先出)队列
*   LIFO(后进先出)队列
*   Priority优先级队列 

这些队列都实现了锁原语，能够在多线程中直接使用，可以使用队列来实现线程间的同步。

Queue 模块中的常用方法:

*   Queue.qsize()：返回队列的大小
*   Queue.empty()：如果队列为空，返回True,反之False
*   Queue.full()： 如果队列满了，返回True,反之False
*   Queue.full 与 maxsize 大小对应
*   Queue.get(\[block\[, timeout\]\])：获取队列，timeout等待时间
*   Queue.get\_nowait()： 相当Queue.get(False)
*   Queue.put(item)： 写入队列，timeout等待时间
*   Queue.put\_nowait(item)： 相当Queue.put(item, False)
*   Queue.task\_done()： 在完成一项工作之后，Queue.task\_done()函数向任务已经完成的队列发送一个信号
*   Queue.join()： 实际上意味着等到队列为空，再执行别的操作

```
import threading      
import time      
import queue       
exitFlag=1       
#多线程类      
class myThread(threading.Thread):      
	def __init__(self,threaddID,name,que):      1
		threading.Thread.__init__(self)      1
		self.threaddID=threaddID      1
		self.name=name      1
		self.que=que      1
	def run(self):      1
		print("开启线程："+self.name)      1
		process_data(self.name,self.que)      1
		print("退出线程："+self.name)       1
def process_data(threadName,que):      2
	while exitFlag:      2
		queueLock.acquire()      2
		if workQueue.empty():      2
			queueLock.release()      2
		else:      2
			data=que.get()      2
			queueLock.release()      2
			print("%s processing %s"%(threadName,data))      2
		time.sleep(1)       3
threadList=["Thread-1","Thread-2","Thread-3"]      3
nameList=["one","Two","Three","Four","Five"]       3
queueLock=threading.Lock()  #获得线程锁定      3
workQueue=queue.Queue(10)  #获得线程队列      3
threads=[]      3
threadID=1       3
#创建多线程线程      3
for tName in threadList:      4
	thread=myThread(threadID,tName,workQueue)      4
	thread.start()      4
	threads.append(thread)      4
	threadID+=1       4
#填充队列      4
queueLock.acquire()      4
for word in nameList:      4
	workQueue.put(word)      4
queueLock.release()       5
#等待队列清空      5
while not workQueue.empty():      5
	pass       5
#通知线程是时候退出      5
exitFlag=0       5
#等待所有线程完成      5
for t in threads:      6
	t.join()      6
print("退出主线程")
```


 ![](https://img-blog.csdnimg.cn/20201007104154608.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[https://www.runoob.com/python3/python3-multithreading.html](https://www.runoob.com/python3/python3-multithreading.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-132)[高级教程](https://edu.csdn.net/skill/python/python-3-132)[线程进程和协程](https://edu.csdn.net/skill/python/python-3-132)84785 人正在系统学习中