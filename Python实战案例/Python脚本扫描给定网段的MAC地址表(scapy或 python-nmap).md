**目录**

[用scapy模块写](#t0)

[用 python-nmap 模块写](#%C2%A0%E7%94%A8%C2%A0python-nmap%C2%A0%E6%A8%A1%E5%9D%97%E5%86%99)

* * *

> python3.7  windows环境

以下两个都可以扫描指定主机或者指定[网段](https://so.csdn.net/so/search?q=%E7%BD%91%E6%AE%B5&spm=1001.2101.3001.7020)的 IP 对应的 MAC 地址，然后保存到 csv 文件中。用scapy模块写的脚本速度很快，几秒就可以全部扫描完，用python-nmap扫描的比较慢，建议用 scapy 模块写的。

### 用scapy模块写

\-H 参数可以是一个ip地址，也可以是一个网段。例：192.168.10.100  或   192.168.10.10-20 或  192.168.10.0/24

```
# -*- coding: utf-8 -*-      
"""      
Created on Wed Nov  7 22:27:55 2018      
@author: 小谢      
"""      
from scapy.all import *      
import csv      
import optparse      
import time      1
def Ip_Mac(host):      1
    try:      1
        ans,unans=srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=host),timeout=2,verbose=False)  #发送ARP请求包,verbose=False的话，就不显示发包信息      1
    except Exception as e:      1
        print("异常对象的类型是:%s"%type(e))      1
        print("异常对象的内容是：%s"%e)      1
    else:      1
        try:      1
            with open("ip.csv","w",newline="") as f:      1
                 csv_writer=csv.writer(f)      2
                 csv_writer.writerow(('IP','MAC'))      2
                 for send,rec in ans:      2
                     ip_mac=rec.sprintf("{ARP:%ARP.psrc%-%Ether.src%}")   #将包按照固定的格式打印      2
                     print(ip_mac)      2
                     mac=ip_mac.split("-")          #去除中间的 - ，返回元组型数据      2
                     csv_writer.writerow(mac)       #将元组写入到文件中      2
        except Exception as e:      2
             print("异常对象的类型是:%s"%type(e))      2
             print("异常对象的内容是：%s"%e)      2
        finally:      3
            f.close()           3
def main():      3
    usage="python %proc -H 192.168.10.10 / 192.168.10.0/24"      3
    parser=optparse.OptionParser(usage)      3
    parser.add_option('-H',dest='Hosts',type='string',help='target hosts')      3
    (options,args)=parser.parse_args()      3
    Hosts=options.Hosts      3
    if Hosts==None:      3
        print(parser.usage)      3
        exit(0)      4
    else:      4
        Ip_Mac(Hosts)      4
main()
```


既可以扫描一个ip地址，也可以扫描一个网段 

### ![](https://img-blog.csdnimg.cn/20181108145329109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###  用 python-nmap 模块写

这个模块扫描特别慢，不建议使用。

\-H 参数可以是一个IP地址或者一个网段。例：192.168.10.100 或192.168.10.10-20 或  192.168.10.0/24

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 19:58:51 2018      
@author: 小谢      
"""      
##列出给定网段的主机的MAC地址表      
import nmap      
import optparse      
import time      1
import csv      1
def ListMac(hosts):      1
    nm=nmap.PortScanner()      1
    nm.scan(hosts)      1
    targets=[]      1
    for t in nm.all_hosts():      1
        if nm[t]['status']['state']=="up":      1
            mac=nm[t]['addresses']['mac']      1
            print("ip: %s  mac: %s"%(t,mac))      1
            targets.append((t,mac))      2
        else:      2
            pass      2
    return targets       2
def main():      2
    usage="python %proc -H 192.168.10.10-20"      2
    parser=optparse.OptionParser(usage)      2
    parser.add_option('-H',dest='Hosts',type='string',help='target hosts')      2
    (options,args)=parser.parse_args()      2
    Hosts=options.Hosts      3
    if Hosts==None:      3
        print(parser.usage)      3
        exit(0)      3
    else:      3
        target=ListMac(Hosts)      3
        try:      3
            with open("ip.csv","w",newline="") as f:      3
                csv_writer=csv.writer(f)      3
                csv_writer.writerow(('IP','MAC'))      3
                for i in target:      4
                    csv_writer.writerow(i)      4
        except Exception as e:      4
                print("异常对象的类型是:%s"%type(e))      4
                print("异常对象的内容是：%s"%e)      4
        finally:      4
               f.close()      4
if __name__=='__main__':      4
    main()
```


![](https://img-blog.csdnimg.cn/2018110815253462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章： [Python中Scapy网络嗅探模块的使用](https://blog.csdn.net/qq_36119192/article/details/83758202)

                   [Python中python-nmap模块的使用](https://blog.csdn.net/qq_36119192/article/details/83717690)