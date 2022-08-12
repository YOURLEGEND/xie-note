**目录**

[HashCat](#t0 "HashCat")

[HshCat的使用](#t1 "HshCat的使用")

[使用Hashcat生成字典](#t2 "使用Hashcat生成字典")

[使用Hashcat破解LM Hash和NTLM Hash](#t3 "使用Hashcat破解LM Hash和NTLM Hash")

[使用Hashcat破解Net-NTLMHash](#t4 "使用Hashcat破解Net-NTLMHash")

* * *

HashCat
-------

HashCat系列软件在硬件上支持使用CPU、NVIDIA [GPU](https://so.csdn.net/so/search?q=GPU&spm=1001.2101.3001.7020)、ATI GPU来进行密码破解。在操作系统上支持Windows、Linux平台，并且需要安装官方指定版本的显卡驱动程序，如果驱动程序版本不对，可能导致程序无法运行。

HashCat主要分为三个版本：Hashcat、oclHashcat-plus、oclHashcat-lite。这三个版本的主要区别是：HashCat只支持CPU破解。oclHashcat-plus支持使用GPU破解多个[HASH](https://so.csdn.net/so/search?q=HASH&spm=1001.2101.3001.7020)，并且支持的算法高达77种。oclHashcat-lite只支持使用GPU对单个HASH进行破解，支持的HASH种类仅有32种，但是对算法进行了优化，可以达到GPU破解的最高速度。如果只有单个密文进行破解的话，推荐使用oclHashCat-lite。

### HshCat的使用

由于笔者穷逼一个，所以使用最简单 cpu 破解。

```
-m   指定哈希类型      
-a   指定攻击模式，有5中模式      
    0 Straight（字典破解）      
    1 Combination（组合破解）      
    3 Brute-force（掩码暴力破解）      
    6 Hybrid dict + mask（混合字典+掩码）      
    7 Hybrid mask + dict（混合掩码+字典）      
-o   输出文件      
-stdout  指定基础文件      1
-r  指定规则文件      1
--force 强制进行破解      1
--show  显示破解结果       1
-V   打印出版本      1
-h   查看帮助
```


\-m 参数的一些[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)类型

![](https://img-blog.csdnimg.cn/20201208195746827.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用Hashcat生成字典

rules目录下存放着生成字典的各种规则

我们在当前目录下将基础信息保存在 base.txt文件中，--stdout 指定基础信息文件，-r 指定规则文件，-o输出成文件。

输出成test.txt文件

```
hashcat64.exe --stdout base.txt -r rules\dive.rule -o test.txt
```


### 使用Hashcat破解LM Hash和NTLM Hash

root对应的LM和NLM哈希如下

![](https://img-blog.csdnimg.cn/20201208201854874.png)

**破解LM Hash**

LM Hash的模式是3000

```
hashcat64.exe -m 3000 d480ea9533c500d4aad3b435b51404ee pass.txt --force
```


 爆破完成后，可以使用 --show 参数查看结果 ，但是要注意爆破LM Hash的结果默认会大写。

![](https://img-blog.csdnimg.cn/20201208202249666.png)

**破解NTLM Hash**

NTLM Hash的模式是1000

```
hashcat64.exe -m 1000 329153f560eb329c0e1deea55e88a1e9 pass.txt --force
```


 爆破完成后，可以使用 --show 参数查看结果

![](https://img-blog.csdnimg.cn/20201208202144437.png)

### 使用Hashcat破解Net-NTLMHash

```
hashcat64.exe -m 5600 xie::WIN7:77effc5381037df8:b6b777ced0128e3f587fe08b98853e13:0101000000000000f27fa3a7aa61d501ba3237c701d9f3970000000002000e00570049004e00320030003000380001000e00570049004e00320030003000380004000e00570049004e00320030003000380003000e00570049004e00320030003000380007000800f27fa3a7aa61d50106000400020000000800300030000000000000000100000000200000ca0ee75c65eaa5367775b826f949798912fa871a19e5d17f9b49587485a8e6620a001000000000000000000000000000000000000900240063006900660073002f003100390032002e003100360038002e00310030002e0031003500000000000000000000000000 pass.txt --force
```


![](https://img-blog.csdnimg.cn/20201208201622931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201208201702235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)