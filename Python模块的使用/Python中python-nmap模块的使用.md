 **目录**

[python-nmap的安装](#python-nmap%E7%9A%84%E5%AE%89%E8%A3%85)

[python-nmap模块的使用](#t0)

[portScanner()类](#t1)

* * *

> 环境：  python 2.7.13

Windows和Linux默认都是不安装python-[nmap](https://so.csdn.net/so/search?q=nmap&spm=1001.2101.3001.7020)的，我们得手动安装

python-nmap的安装
--------------

**linux:**

*   wge t http://xael.org/pages/python-nmap-0.6.1.tar.gz
*   tar  -zxvf  python-nmap-0.6.1.tar.gz
*   cd  python-nmap-0.6.1
*   python setup.py install

**Windows:**   pip  install  python-nmap

python-nmap模块的使用
----------------

### portScanner()类

portScanner()类用于实现对指定主机进行端口扫描

```
nmScan=nmap.PortScanner()  #实例化
```


**scan( host , port , args )** 方法：以指定方式扫描指定主机或网段的指定端口

参数：

*   host ： 要扫描的主机或网段，可以是一个单独的ip：192.168.10.10 ；也可以是一个小范围网段：192.168.10.10-20 ；也可以是一个大网段：192.168.10.0/24
*   port :    可选参数，要扫描的端口，多个端口用逗号隔开，如：20,21,22,23,24
*   args ：可选参数，要扫描的方式

```
import nmap      
nm = nmap.PortScanner()      
nm.scan('192.168.10.10-100', '22,21','-sV')       
也可以这样      
nm.scan(hosts='192.168.1.0/24', arguments='-n -sP -PE -PA21,23,80,3389')
```


**command\_line()** 方法：返回的扫描方法映射到具体的nmap命令行，也就是我们上面执行的nm.scan()命令，对应的具体的nmap的命令

```
import nmap      
nm = nmap.PortScanner()      
nm.scan('192.168.10.10-100', '22,21','-sV')       
a=nm.command_line()      
print(a)      
###########################      
nmap -oX - -p 20,21 -sV 192.168.125.134
```


**scaninfo()** 方法：返回nmap扫描信息，格式为字典类型

```
import nmap      
nm = nmap.PortScanner()      
nm.scan('192.168.10.10-100', '22,21','-sV')      
a=nm.scaninfo()      
print(a)      
###########################      
{'tcp': {'services': '20-21', 'method': 'syn'}}
```


**all\_hosts()** 方法：返回nmap扫描的主机清单，格式为列表类型

```
import nmap      
nm = nmap.PortScanner()      
nm.scan('192.168.10.10-12', '22,21','-sV')      
####################################################      
['192.168.10.10','192.168.10.11','192.168.10.12']
```


**查看扫描主机的详细信息： nm\['192.168.125.134'\]**

```
import nmap      
nm=nmap.PortScanner()      
nm.scan('192.168.125.134','445')      
a=nm['192.168.125.134']    #返回主机的详细信息      
print(a)      
##################################      
{'status': {'state': 'up', 'reason': 'arp-response'},       
'hostnames': [{'type': 'PTR', 'name': 'bogon'}],       
'vendor': {'00:0C:29:F6:2B:F0': 'VMware'},       1
'addresses': {'mac': '00:0C:29:F6:2B:F0', 'ipv4': '192.168.125.134'},       1
'tcp': {445: {'product': 'Microsoft Windows 7 - 10 microsoft-ds', 'state': 'open', 'version': '', 'name': 'microsoft-ds', 'conf': '10', 'extrainfo': 'workgroup: WORKGROUP', 'reason': 'syn-ack', 'cpe': 'cpe:/o:microsoft:windows'}}}
```


**查看主机扫描的端口包含的协议（TCP/UDP）：nmap\['192.168.125.134'\].all\_protocols()  返回列表型数据**

```
import nmap      
nm=nmap.PortScanner()      
nm.scan('192.168.125.134','445')      
a=nm['192.168.125.134'].all_protocols()    #返回主机扫描的端口包含的协议      
print(a)      
##################################      
['tcp']
```


**查看扫描的端口哪些端口提供了TCP协议：nmap\['192.168.125.134'\]\['tcp'\]**

```
import nmap      
nm=nmap.PortScanner()      
nm.scan('192.168.125.134','445')      
a=nm['192.168.125.134']['tcp']        #返回主机扫描的端口哪些端口包含了TCP协议，返回具体信息      
b=nm['192.168.125.134']['tcp'].keys()  #只返还端口,返回列表型数据      
print(a)      
print(b)      
##################################      
{445: {'product': 'Microsoft Windows 7 - 10 microsoft-ds', 'state': 'open', 'version': '', 'name': 'microsoft-ds', 'conf': '10', 'extrainfo': 'workgroup: WORKGROUP', 'reason': 'syn-ack', 'cpe': 'cpe:/o:microsoft:windows'}}      1
[445]
```


**查看该端口是否提供了TCP协议：nmap\['192.168.125.134'\].has\_tcp(445) ，返回布尔类型的数据**

```
import nmap      
nm=nmap.PortScanner()      
nm.scan('192.168.125.134','445')      
a=nm['192.168.125.134'].has_tcp(445)        #判断该端口是否提供TCP协议      
print(a)      
##################################      
True
```


**查看该协议下端口的状态：nmap\['192.168.125.134'\]\['tcp'\]\[445\]\[state\]**

```
import nmap      
nm=nmap.PortScanner()      
nm.scan('192.168.125.134','445')      
a=nm['192.168.125.134']['tcp'][445]['state']      
print(a)      
##################################      
open
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-6)[预备知识](https://edu.csdn.net/skill/python/python-3-6)[模块管理](https://edu.csdn.net/skill/python/python-3-6)84711 人正在系统学习中