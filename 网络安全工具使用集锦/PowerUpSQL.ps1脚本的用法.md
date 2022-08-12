PowerUpSQL
----------

PowerUpSQL是为攻击SQLServer而设计的具有攻击性的[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)脚本。PowerUpSQL包括支持SQLServer发现、弱配置审核、权限升级和利用后操作(如OS命令执行)的功能，它常用于内网渗透。PowerUpSQL还包括许多功能，管理员可以使用这些功能快速地在AD域中查询SQL服务器，并执行与SQLServer相关的常见威胁查找任务。项目地址：[https://github.com/NetSPI/PowerUpSQL](https://github.com/NetSPI/PowerUpSQL "https://github.com/NetSPI/PowerUpSQL")

### PowerUpSQL的使用

首选，导入该模块

```
Import-Module .\PowerUpSQL.ps1
```


发现SQLServer实例

```
Get-SQLInstanceLocal          #发现本机SQLServer实例      
Get-SQLInstanceDomain         #发现域中的SQLServer实例      
Get-SQLInstanceBroadcast      #通过广播PING发现SQLServer实例
```


对发现SQLServer实例，进行进一步操作。这里我们域为例，| 符号是将前面的输出作为后面的输入

```
Get-SQLInstanceDomain | Get-SQLServerConfiguration     #将发现的SQLServe实例全部查看其配置信息      
Get-SQLInstanceDomain | Get-SQLServerInfo              #将发现的SQLServe实例全部查看其服务信息      
Get-SQLInstanceDomain | Get-SQLServerLogin             #将发现的SQLServe实例全部查看其登录信息      
Get-SQLInstanceDomain | Get-SQLConnectionTest          #检测发现的SQLServer实例的可访问性
```


也可以指定SQLServer实例的名字进行检测

```
Get-SQLServerConfiguration -Instance SQLServerTest   #查看实例SQLServerTest的配置信息      
Get-SQLServerInfo -Instance SQLServerTest            #查看实例SQLServerTest的服务信息      
Get-SQLServerLogin -Instance SQLServerTest           #查看实例SQLServerTest的登录信息      
Get-SQLConnectionTest -Instance SQLServerTest        #查看实例SQLServerTest是否可访问
```


查看实例 SQLServerTest 是否配置错误

```
Invoke-SQLAudit -Instance SQLServerTest -verbose
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[PowerUpSQL工具的使用](https://www.baidu.com/link?url=1Kyw-NPeRZSotTHV1ps9DoU0JHpNUjWZ-EwPv8-rREMb4204h_VityBkVzsPssT0&wd=&eqid=c32d30710035c27f000000035dea70ba "PowerUpSQL工具的使用")

                  [利用PowerUpSQL攻击SQL Server实例](https://www.freebuf.com/column/172122.html "利用PowerUpSQL攻击SQL Server实例")