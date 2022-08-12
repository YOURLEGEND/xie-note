**目录**

[协程](#t0)

[python 进行并发编程](#t1)

[小结](#t2)

* * *

### 协程

在python GIL之下，同一时刻只能有一个线程在运行，那么对于CPU计算密集的程序来说，线程之间的切换开销就成了拖累，而以I/O为瓶颈的程序正是[协程](https://so.csdn.net/so/search?q=%E5%8D%8F%E7%A8%8B&spm=1001.2101.3001.7020)所擅长的。

**协程** ，又称为微线程，它是实现多任务的另一种方式，只不过是比线程更小的执行单元。因为它自带CPU的上下文，这样只要在合适的时机，我们可以把一个协程切换到另一个协程。

**通俗的理解：** 在一个线程中的某个函数中，我们可以在任何地方保存当前函数的一些临时变量等信息，然后切换到另外一个函数中执行，注意不是通过调用函数的方式做到的 ，并且切换的次数以及什么时候再切换到原来的函数都由开发者自己确定。

**协程与线程的差异：**在实现多任务时, 线程切换从系统层面远不止保存和恢复CPU上下文这么简单。操作系统为了程序运行的高效性，每个线程都有自己缓存Cache等等数据，操作系统还会帮你做这些数据的恢复操作，所以线程的切换非常耗性能。但是协程的切换只是单纯地操作CPU的上下文，所以一秒钟切换上百万次系统都抗的住。

Python中的协程经历了很长的一段发展历程。其大概经历了如下三个阶段：

1.  最初的生成器变形yield/send
2.  引入@asyncio.coroutine和yield from
3.  在最近的Python3.5版本中引入async/await关键字

（1）从yield说起

先看一段普通的计算斐波那契续列的代码

```
def fibs(n):      
   res = [0] * n      
   index = 0      
   a = 0      
   b = 1      
   while index < n:      
      res[index] = b      
      a, b = b, a + b      
      index += 1      1
   return res       1
for fib_res in fibs(20):      1
   print(fib_res)
```


​ 如果我们仅仅是需要拿到斐波那契序列的第n位，或者仅仅是希望依此产生斐波那契序列，那么上面这种传统方式就会比较耗费内存。

这时，yield就派上用场了。

```
def fib(n):      
   index = 0      
   a = 0      
   b = 1      
   while index < n:      
      yield b      
      a, b = b, a + b      
      index += 1       1
for fib_res in fib(20):      1
   print(fib_res)
```


​ 当一个函数中包含yield语句时，python会自动将其识别为一个生成器。这时fib(20)并不会真正调用函数体，而是以函数体生成了一个生成器对象实例。

​ yield在这里可以保留fib函数的计算现场，暂停fib的计算并将b返回。而将fib放入for…in循环中时，每次循环都会调用next(fib(20))，唤醒生成器，执行到下一个yield语句处，直到抛出StopIteration异常。此异常会被for循环捕获，导致跳出循环。

(2) Send来了

​ 从上面的程序中可以看到，目前只有数据从fib(20)中通过yield流向外面的for循环；如果可以向fib(20)发送数据，那不是就可以在Python中实现协程了嘛。

​ 于是，Python中的生成器有了send函数，yield表达式也拥有了返回值。

​ 我们用这个特性，模拟一个慢速斐波那契数列的计算：

```
import time      
import random       
def stupid_fib(n):      
   index = 0      
   a = 0      
   b = 1      
   while index < n:      
      sleep_cnt = yield b      1
      print('let me think {0} secs'.format(sleep_cnt))      1
      time.sleep(sleep_cnt)      1
      a, b = b, a + b      1
      index += 1       1
print('-' * 10 + 'test yield send' + '-' * 10)      1
N = 20      1
sfib = stupid_fib(N)      1
fib_res = next(sfib) #第一次必须要执行next（）函数，让程序控制到yield b 位置      2
while True:      2
   print(fib_res)      2
   try:      2
      fib_res = sfib.send(random.uniform(0, 0.5))      2
   except StopIteration:      2
      break
```


### python 进行并发编程

​ 在Python 2的时代，高性能的网络编程主要是使用Twisted、Tornado和Gevent这三个库，但是它们的异步代码相互之间既不兼容也不能移植。

​ asyncio是Python 3.4版本引入的标准库，直接内置了对异步IO的支持。

​ `asyncio`的编程模型就是一个消息循环。我们从`asyncio`模块中直接获取一个`EventLoop`的引用，然后把需要执行的协程扔到`EventLoop`中执行，就实现了异步IO。

​ Python的在3.4中引入了协程的概念，可是这个还是以生成器对象为基础。

​ Python 3.5添加了async和await这两个关键字，分别用来替换`asyncio.coroutine`和`yield from`。

​ python3.5则确定了协程的语法。下面将简单介绍asyncio的使用。实现协程的不仅仅是asyncio，tornado和gevent， vloop都实现了类似的功能。

（1）协程定义

用`asyncio`实现`Hello world`代码如下：

```
import asyncio       
@asyncio.coroutine      
def hello():      
    print("Hello world!")      
    # 异步调用asyncio.sleep(1)-->协程函数:      
    r = yield from asyncio.sleep(1)  #此处为另外一个协程，不是休眠      
    print("Hello again!")       1
# 获取EventLoop（事件循环器）:      1
loop = asyncio.get_event_loop()      1
# 执行coroutine      1
loop.run_until_complete(hello())      1
loop.close()
```


@asyncio.coroutine`把一个generator标记为coroutine类型，然后，我们就把这个`coroutine`扔到`EventLoop`中执行。 hello()`会首先打印出`Hello world!`，然后，`yield from`语法可以让我们方便地调用另一个`generator`。由于`asyncio.sleep()`也是一个`coroutine`，所以线程不会等待`asyncio.sleep()`，而是直接中断并执行下一个消息循环。当`asyncio.sleep()`返回时，线程就可以从`yield from`拿到返回值（此处是`None`），然后接着执行下一行语句。

​ 把`asyncio.sleep(1)`看成是一个耗时1秒的IO操作，在此期间，主线程并未等待，而是去执行`EventLoop`中其他可以执行的`coroutine`了，因此可以实现并发执行。

我们用Task封装两个`coroutine`试试：

```
import threading      
import asyncio       
@asyncio.coroutine      
def hello():      
    print('Hello world! (%s)' % threading.currentThread())      
    yield from asyncio.sleep(1)      
    print('Hello again! (%s)' % threading.currentThread())       1
loop = asyncio.get_event_loop()      1
tasks = [hello(), hello()]      1
loop.run_until_complete(asyncio.wait(tasks))      1
loop.close()
```


观察执行过程：

```
Hello world! (<_MainThread(MainThread, started 140735195337472)>)      
Hello world! (<_MainThread(MainThread, started 140735195337472)>)      
(暂停约1秒)      
Hello again! (<_MainThread(MainThread, started 140735195337472)>)      
Hello again! (<_MainThread(MainThread, started 140735195337472)>)
```


由打印的当前线程名称可以看出，两个`coroutine`是由同一个线程并发执行的。

如果把`asyncio.sleep()`换成真正的IO操作，则多个`coroutine`就可以由一个线程并发执行。

asyncio案例实战

我们用`asyncio`的异步网络连接来获取sina、sohu和163的网站首页：

**async\_wget.py**

```
import asyncio       
@asyncio.coroutine      
def wget(host):      
    print('wget %s...' % host)      
    connect = asyncio.open_connection(host, 80) #等待打开host:80端口      
    reader, writer = yield from connect #开始链接。如果连接成功，则返回Reader和写writer的操作对象      
    header = 'GET / HTTP/1.0\r\nHost: %s\r\n\r\n' % host      
    writer.write(header.encode('utf-8'))      1
    yield from writer.drain()      1
    while True:      1
        line = yield from reader.readline()      1
        if line == b'\r\n':      1
            break      1
        print('%s header > %s' % (host, line.decode('utf-8').rstrip()))      1
    # Ignore the body, close the socket      1
    writer.close()       1
loop = asyncio.get_event_loop()      2
tasks = [wget(host) for host in ['www.sina.com.cn', 'www.sohu.com', 'www.163.com']]      2
loop.run_until_complete(asyncio.wait(tasks))      2
loop.close()
```


结果信息如下：

```
wget www.sohu.com...      
wget www.sina.com.cn...      
wget www.163.com...      
(等待一段时间)      
(打印出sohu的header)      
www.sohu.com header > HTTP/1.1 200 OK      
www.sohu.com header > Content-Type: text/html      
...      
(打印出sina的header)      1
www.sina.com.cn header > HTTP/1.1 200 OK      1
www.sina.com.cn header > Date: Wed, 20 May 2015 04:56:33 GMT      1
...      1
(打印出163的header)      1
www.163.com header > HTTP/1.0 302 Moved Temporarily      1
www.163.com header > Server: Cdn Cache Server V2.0      1
...
```


可见3个连接由一个线程通过`coroutine`并发完成。

（3） 使用async/await

```
import asyncio      
import re       
async def browser(host, port=80):      
    # 连接host      
    reader, writer = await asyncio.open_connection(host, port)      
    print(host, port, '连接成功!')       
    # 发起 / 主页请求(HTTP协议)      1
    # 发送请求头必须是两个空行      1
    index_get = 'GET {} HTTP/1.1\r\nHost:{}\r\n\r\n'.format('/', host)      1
    writer.write(index_get.encode())       1
    await writer.drain()  # 等待向连接写完数据（请求发送完成）       1
    # 开始读取响应的数据报头      1
    while True:      1
        line = await reader.readline()  # 等待读取响应数据      1
        if line == b'\r\n':      2
            break       2
        print(host, '<header>', line)       2
    # 读取响应的数据body      2
    body = await reader.read()      2
    print(encoding)      2
    print(host, '<content>', body)       3
if __name__ == '__main__':      3
    loop = asyncio.get_event_loop()       3
    tasks = [browser(host) for host in ['www.dushu.com', 'www.sina.com.cn', 'www.baidu.com']]       3
    loop.run_until_complete(asyncio.wait(tasks))      3
    loop.close()       3
    print('---over---')
```


### 小结

`asyncio`提供了完善的异步IO支持；

异步操作需要在`coroutine`中通过`yield from`完成；

多个`coroutine`可以封装成一组Task然后并发执行。

参考文章：[https://blog.csdn.net/weixin\_41599977/article/details/93656042](https://blog.csdn.net/weixin_41599977/article/details/93656042)

                 [https://blog.csdn.net/daiyu\_\_zz/article/details/81912018](https://blog.csdn.net/daiyu__zz/article/details/81912018)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-132)[高级教程](https://edu.csdn.net/skill/python/python-3-132)[线程进程和协程](https://edu.csdn.net/skill/python/python-3-132)84785 人正在系统学习中