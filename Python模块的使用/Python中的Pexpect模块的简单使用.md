> Pexpect 是一个用来启动子程序并对其进行自动控制的 Python 模块。 Pexpect 可以用来和像 [ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)、ftp、passwd、telnet 等命令行程序进行自动交互。以下所有代码都是在Kali Linux环境下，python 2.7.13

Pexpect中的 pexpect.spawn() 和 pexpect.run() 依赖于Python标准库中的 pty 模块，而pty模块只在POSIX系统中存在，所以在Windows系统上的功能有限。

Pexpect模块首先通过生成子应用以代理交互应用，这样就可以通过检测子应用的模式匹配情况以响应交互应用的输出。

对于以下代码：

connect()函数接收的参数包括一个用户名、主机名和密码，返回SSH的连接结果。利用Pexpect库，程序会等待一个可预计到的输出。如：超时、公钥信息改变或要求输入密码。如果出现超时，session.expect()返回零，打印一个错误消息后返回。如果child.expect()方法捕获了ssh\_newkey的消息，它会返回数字1，使函数发送 yes 消息以接收新秘钥，之后等待密码提示符发送SSH密码。一旦通过验证，我们就可以使用一个单独的command()函数在SSH会话中发送命令。command()函数需要接收的参数是一个SSH会话和命令字符串，然后，它向会话发送命令字符串，并等待命令提示符再次出现，在获得命令提示符后，该函数把从SSH会话处得到的结果打印出来

```
import pexpect       
PROMPT=['#','>>>','>','\$']      
def send_command(child,cmd):        #传递命令      
    child.sendline(cmd)      
    child.expect(PROMPT)            #期望获得的命令提示符      
    print child.before              ##打印从SSH会话得到的结果      
def connect(user,host,password):       
    ssh_newkey="Are you sure you want to continue connecting"      1
    connStr='ssh '+user+'@'+host      1
    child=pexpect.spawn(connStr)        #实例化连接      1
    ret=child.expect([pexpect.TIMEOUT,ssh_newkey,'[P|p]assword:'])  ##捕获 ssh_newkey      1
    if ret==0:                            #判断捕获的信息      1
        print '[-] Error Connecting'      1
        return      1
    if ret==1:                           #捕获了ssh_newkey的消息      1
        child.sendline('yes')            #发送yes      1
        ret=child.expect([pexpect.TIMEOUT,ssh_newkey,'[P|p]assword:'])      1
        if ret==0:      2
            print '[-] Error Connecting'      2
            return      2
    child.sendline(password)      2
    child.expect(PROMPT)                            #捕获命令提示符      2
    return child      2
def main():      2
    host="192.168.10.10"      2
    user="root"      2
    password="toor"      2
    child=connect(user,host,password)      3
    send_command(child,'ls /root/')      3
if __name__=='__main__':      3
    main()
```


相关文章：[Python脚本暴力破解SSH口令](https://blog.csdn.net/qq_36119192/article/details/83691976)

                  [Pexpect模块的使用](https://www.ibm.com/developerworks/cn/linux/l-cn-pexpect2/)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-6)[预备知识](https://edu.csdn.net/skill/python/python-3-6)[模块管理](https://edu.csdn.net/skill/python/python-3-6)84711 人正在系统学习中