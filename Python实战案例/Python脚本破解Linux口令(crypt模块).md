> 环境 [Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020) Linux ，python版本2.7.13 。

我们利用Linux系统中的 **crypt** 模块模拟了Linux系统中用户密码的[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)，在Windows中是不存在这个库的。

![](https://img-blog.csdnimg.cn/20181030172127250.png)

在Linux系统中，用户的密码被加密存储在了 /etc/[shadow](https://so.csdn.net/so/search?q=shadow&spm=1001.2101.3001.7020) 文件中。

如下是 /etc/shadow中root用户的字段

```
root:$6$ql1UU7ZPwONL9NzX$/bz0GQ8.Ne.zqpF.L5rqPok.Zep0ypRU1X8v.Omrqnv1k6hVDzqTS1Vezencaxltk52X94eZMT/teVHI1Jwf6.:17767:0:99999:7:::
```


第二个字段是密码部分的，我们把它单独拿出来

```
$6$ql1UU7ZPwONL9NzX$/bz0GQ8.Ne.zqpF.L5rqPok.Zep0ypRU1X8v.Omrqnv1k6hVDzqTS1Vezencaxltk52X94eZMT/teVHI1Jwf6.
```


密码部分的格式为：$id$salt$encrypted  

*   id是指用的哈希算法，id为1是MD5，id为5是SHA-256，id为6是SHA-512
*   salt 为盐值
*   encrypted 为hash值，这里的hash值是密码和盐值一起加密之后得到的

在了解了shadow 文件中密码的加密方式之后，我们就可以开始编写python脚本了。

在编写之前，我们需要准备好密码字典文件 key.txt 。只有当密码字典足够大时，才有可能破解出密码。

然后我们将 /etc/shadow 文件复制到python脚本所在目录，修改文件名为 shadow.txt

在编写时，我们创建两个函数：main() 和 testPass() 。main() 函数打开 shadow.txt 文件中的内容，读取其中的用户名字段和密码字段cryptPass。testPass() 函数将密码字段中的 $id$salt 提取出来赋值给 salt 。然后读取 key.txt 中的密码字段，赋值给 word 。再利用Linux系统中python的一个crypt库，将word和salt一起作为参数传进去进行加密，得到 cryptWord 。最后将cryptWord和cryptPass进行对比，如果相等，则这个word就是该用户的密码，否则不是。

```
# -*- coding: utf-8 -*-      
"""      
Created on Mon Oct 29 15:01:54 2018      
@author: 小谢      
"""      
import crypt   ##导入Linux口令加密库      
def testPass(cryptPass):      
    salt=cryptPass[cryptPass.find("$"):cryptPass.rfind("$")]  ##获得盐值，包含$id部分      
    dictFile=open('key.txt','r')      1
    for word in dictFile.readlines():      1
        word=word.strip("\n")      1
        cryptWord=crypt.crypt(word,salt)                   ##将密码字典中的值和盐值一起加密      1
        if (cryptWord==cryptPass):                           ##判断加密后的数据和密码字段是否相等      1
            print "[+]Found Password:"+word+"\n"       ##如果相等则打印出来      1
            return       1
    print "[-] Password Not Found.\n"      1
    return        1
def main():      2
    passFile=open('shadow.txt')      2
    for line in passFile.readlines():      ##读取文件中的所有内容      2
        if ":" in line:      2
            user=line.split(":")[0]                     ##获得用户名      2
            cryptPass=line.split(":")[1].strip(' ')   ##获得密码字段      2
            print "[*] Cracking Password for:"+user      2
            testPass(cryptPass)      2
main()
```


脚本运行截图，破解出了用户tom和用户xie的密码。 

![](https://img-blog.csdnimg.cn/20181030171917147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)