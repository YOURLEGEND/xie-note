**目录**

[Socket模块编写](#t0)

[扫描给定主机是否开放了指定的端口](#t1)

[python-nmap模块编写](#t2) 

[扫描给定ip或给定网段内指定端口是否开放](#t3)

* * *

> 一个用python写的简单的端口扫描器，python环境为 3.7.0，windows系统

Socket模块编写
----------

### 扫描给定主机是否开放了指定的端口

TCP连接扫描是利用TCP的[三次握手](https://so.csdn.net/so/search?q=%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B&spm=1001.2101.3001.7020)来确定主机的端口是否开放。确定主机端口开放之后，给端口发送消息，接收端口返回的消息，然后判断该端口运行的服务。

使用时，-H 参数可以提供主机的域名或者ip地址，-p/-P 写要扫描的端口，多个端口用逗号分隔

```
# -*- coding: utf-8 -*-      
"""      
Created on Sat Nov  3 16:44:28 2018      
@author: 小谢      
"""      
import optparse      
from socket import *      
import threading      
threadLock=threading.Lock()    #实例化threadLock对象      1
def connScan(Host,Port):      1
    try:      1
        conn=socket(AF_INET,SOCK_STREAM)      1
        conn.connect((Host,Port))      1
        conn.send('test message'.encode("utf-8")) #发送测试信息给端口      1
        results=conn.recv(100)                    #接收主机返回的信息      1
        threadLock.acquire()                      #加锁      1
        print('[+]%d/tcp open'% Port)      1
        print('[+] '+results.decode("utf-8"))      1
        conn.close()      2
    except Exception as e:      2
        threadLock.acquire()      2
        print(e)      2
        print('[-]%d/tcp closed'% Port)      2
    finally:      2
        threadLock.release()           #释放锁      2
        conn.close()      2
def portScan(Host,Ports):      2
    try:      2
        IP=gethostbyname(Host)          ##获得对应主机的ip地址      3
    except:      3
        print("[-] Cannot resolve '%s':Unknown host" %Host)      3
        return      3
    try:      3
        Name=gethostbyaddr(Host)     ##获得ip对应主机的信息      3
        print ("\n[+] Scan Results for:"+Name[0])      3
    except:      3
        print ("\n[+] Scan Results for:"+IP)      3
    setdefaulttimeout(1)      3
    for Port in Ports:      4
        print ("Scanning port "+Port)      4
        connScan(Host,int(Port))      4
def main():      4
    usage="usage %prog -H <target host> -p/-P <target ports>"      4
    parser=optparse.OptionParser(usage)  #创建对象实例      4
    parser.add_option('-H',dest='Host',type='string',help='target host')   ##需要的命令行参数      4
    parser.add_option('-P','-p',dest='Ports',type='string',help='target ports')      4
    (options,args)=parser.parse_args()      4
    Host=options.Host      4
    Ports=str(options.Ports).split(',')      5
    if (Host==None)|(Ports==None):         ##如果主机和端口都是空的话      5
        print(parser.usage)      5
        exit(0)      5
    portScan(Host,Ports)      5
if __name__=='__main__':      5
    main()
```


![](https://img-blog.csdnimg.cn/20181103201654186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

python-nmap模块编写 
----------------

我们还可以通过调用[nmap](https://so.csdn.net/so/search?q=nmap&spm=1001.2101.3001.7020)进行端口扫描。

### 扫描给定ip或给定网段内指定端口是否开放

\-H 参数可以扫描一个ip地址也可以扫描一个[网段](https://so.csdn.net/so/search?q=%E7%BD%91%E6%AE%B5&spm=1001.2101.3001.7020)，-p 只能指定一个端口

```
# -*- coding: utf-8 -*-      
"""      
Created on Thu Nov  8 14:26:15 2018      
@author: 小谢      
"""      
import nmap      
import optparse       
def Test(Hosts,port):      1
    nm=nmap.PortScanner()      1
    nm.scan(Hosts,port)      1
    port_int=int(port)                            #将端口从字符串转化为int类型的      1
    for t in nm.all_hosts():      1
        if nm[t].has_tcp(port_int):                #如果445端口提供了TCP协议      1
            state=nm[t]['tcp'][port_int]['state']  #判断该TCP 445 端口的状态      1
            if state=='open':      1
                print ('[+]Found Target Host:'+t)      1
    return      1
def main():      2
    usage="usage %prog -H <target host> -p/-P <target port>"      2
    parser=optparse.OptionParser(usage)  #创建对象实例      2
    parser.add_option('-H',dest='Host',type='string',help='target host')   ##需要的命令行参数      2
    parser.add_option('-P','-p',dest='Port',type='string',help='target ports')      2
    (options,args)=parser.parse_args()      2
    Hosts=options.Host      2
    Port=options.Port      2
    if (Hosts==None)|(Port==None):         #如果主机和端口都是空的话      2
        print(parser.usage)      2
        exit(0)                           #退出      3
    else:      3
        Test(Hosts,Port)      3
if __name__=='__main__':      3
    main()
```


![](https://img-blog.csdnimg.cn/2018110814462232.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关知识点：[Python中的optparse模块的使用](https://blog.csdn.net/qq_36119192/article/details/83661848)

                     [Python中的socket网络模块](https://blog.csdn.net/qq_36119192/article/details/83662680) 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-257)[高级教程](https://edu.csdn.net/skill/python/python-3-257)[函数](https://edu.csdn.net/skill/python/python-3-257)84853 人正在系统学习中