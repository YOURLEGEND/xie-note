**目录**

[PowerShell的帮助系统](#t0 "PowerShell的帮助系统")

[使用帮助系统查找命令](#t1 "使用帮助系统查找命令")

[about关于主题命令](#t2 "about关于主题命令")

[alias别名](#t3 "alias别名")

[使用帮助系统查看指定命令的详细用法](#t4 "使用帮助系统查看指定命令的详细用法")

[参数名称缩写](#t5 "参数名称缩写")

[可选参数和必选参数](#t6 "可选参数和必选参数")

[位置参数](#t7 "位置参数")

[参数值](#t8 "参数值")

[通用参数](#t9 "通用参数")

[show-command图形化输入参数](#t10 "show-command图形化输入参数")

[实践](#t11 "实践")

* * *

PowerShell的帮助系统
===============

可以输入 Get-Help 、help 或 man 来查看帮助系统。help 是对 Get-help 进行封装的函数，而 man 是 help 的别名。Get-Help 和 help 的区别在于，help可以以分页的形式阅读输出，而Get-help则是一次性把所有输出都显示出来。但是两者的功能是一样的，只是对结果的显示不一样。

```
Get-Alias -Definition "help"
```


![](https://img-blog.csdnimg.cn/20210106110719863.png)

[powershell](https://so.csdn.net/so/search?q=powershell&spm=1001.2101.3001.7020)的帮助文档是可以经常更新的。我们可以使用命令：update-help 或 update-help -force 来更新powershell的帮助文档。

![](https://img-blog.csdnimg.cn/20210104100750151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用帮助系统查找命令
----------

例如，想查看操作系统事件日志的命令，我们可以运行如下命令来进行查询：

```
Get-help *log*   或   Get-help *event*
```


![](https://img-blog.csdnimg.cn/20210104101718651.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210104101835381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

其实，Get-help只是查找帮助文档的命令。它只知道哪些命令有可用的帮助文档，但是某些命令可以没有帮助文档，这就会导致使用上面的命令有时候查询不到我们想要的命令。但是，微软所发布的每个命令都包含一个帮助文档，这就意味着上面的命令几乎完全可以找到我们想要的命令。

事实上，我们还可以使用 Get-Command 命令来搜索命令，该命令是专门用来搜索命令的。

如下，该命令可以搜索所有命令中包含event的命令。其中会包含 Function、Cmdlet 和 Application

```
Get-Command *event*
```


![](https://img-blog.csdnimg.cn/20210104103100675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由于搜索的结果类型会有 Function、Cmdlet 和  Application。所以我们可以使用-Type类型来进行过滤

```
Get-Command *event* -type cmdlet
```


 ![](https://img-blog.csdnimg.cn/20210104103228554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

也可以使用参数 -noun 或 -verb 来进行命令查找。-noun 指定查询名词，而 -verb 指定查询动词。

```
Get-Command -noun *event*
```


![](https://img-blog.csdnimg.cn/20210104103431553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
Get-Command -verb get
```


![](https://img-blog.csdnimg.cn/20210104103503590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### about关于主题命令

在帮助系统中，关于 这个主题非常重要，是为了说明其他东西的命令。但是，因为它们没有关联到某个特定的cmdlet命令，所以很容易被人忽略。如果你运行help about\* 列出关于的主题。

![](https://img-blog.csdnimg.cn/20210105001655619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### alias别名

有时候命令太长的话，可以为命令设置别名alias。可以使用 New-Alias 创建自定义别名，当我们自己创建一个别名时，其有效生命周期是持续到当前的shell结束。可以使用 Export-Alias 导出系统的别名列表。可以使用。Get-Alias -Definition "cmdlet"  查询某个cmdlet是否有别名。使用 Get-Alias 获取所有别名列表

```
获取help的别名      
Get-Alias -Definition "help"      
获取所有的别名列表      
Get-Alias      
给notepad设置别名np      
New-Alias np notepad
```


![](https://img-blog.csdnimg.cn/2021010613115827.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210106131629943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用帮助系统查看指定命令的详细用法
-----------------

假如在上一步，我们发现可能 Get-EventLog 命令就是我们需要找的命令。那么接下来，我们该如何查看该命令的详细用法呢？执行如下命令，查看 Get-EventLog 的用法：

```
help Get-EventLog      
help Get-EventLog -full       详细查看帮助，它会显示每个参数的具体的用法，以及是否必须等      
help Get-EventLog -example    只显示示例
```


![](https://img-blog.csdnimg.cn/20210104230509404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)   
![](https://img-blog.csdnimg.cn/20210104232852357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![](https://img-blog.csdnimg.cn/20210105000226761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **参数名称缩写**

如上面所示，-LogName 参数我们可以缩写为 -Log 参数，因为没有其他参数以 -Log 开头，只有 -LogName 参数。而不能使用 -L 参数，因为使用-L开头的参数有 -LogName 和 -List 。

```
Get-EventLog -LogName Application -Newest 5      
Get-EventLog -Log Application -Newest 5      
Get-EventLog -Lo Application -Newest 5
```


![](https://img-blog.csdnimg.cn/20210104232344146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **可选参数和必选参数**

Powershell的帮助文档把可选参数的值类型放到一个方括号\[ \]中，如上面的：\[-ComputerName <string\[\]>\] ，而参数值类型没有放到方括号的就是必选参数，如上面的：\[-LogName\] <string> 

或者我们使用 -full 参数查看详细帮助的时候，这里会提示该参数是否必须

![](https://img-blog.csdnimg.cn/20210104233159107.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210104233218789.png)

### **位置参数**

有时候，某些参数会被频繁的使用，但是我们又不希望每次都输入该参数名称。通常来说，参数是具有位置性的。这意味着我们只需要把参数值放在正确的位置上，就可以只输入该参数值，而不输入该参数。

有两种方法可以用于确定位置参数： **通过详细的帮助文档 或 通过语法概要**。

**在帮助文档中找到位置参数**

直接使用 -full 参数查看详细帮助的时候，这里会提示该参数是否是位置参数，以及是第几个位置

![](https://img-blog.csdnimg.cn/20210104233317164.png)

![](https://img-blog.csdnimg.cn/20210104233345535.png)

![](https://img-blog.csdnimg.cn/20210104233422905.png)

**在语法概要中找到位置参数**

如上面查询的Get-EventLog的用法

```
Get-EventLog [-LogName] <string> [[-InstanceId] <Int64[]>] [-After <DateTime>] [-AsBaseObject] [-Before <DateTime>] [-ComputerName <string[]>] [-EntryType <string[]>] [-Index <Int32[]>] [-Message <string>] [-Newest <int>] [-Source <string[]>] [-UserName <string[]>] [<CommonParameters>]
```


*   第一个参数：-LogName。由于参数值类型和参数值不在同一个方括号内，所以我们可以得知它是必选参数。但是由于它的参数名称在一个方括号内，这让它成为了一个位置参数，所以我们可以只提供日志名称而不需要输入参数名称 -LogName 。并且由于该参数出现在帮助文档的第一个位置，所以我们知道这个日志名称是我们必须提供的第一个参数。
*   第二个参数：-InstanceId。它是可选的，因为它的参数名称与参数值类型位于同一个方括号内。在方括号内，-InstanceId 本身又处于一个方括号内，这就意味着它还是一个位置参数。它出现在第二个位置，所以我们省略这个参数名称，就必须在该位置提供一个参数值。
*   第三个参数：-Afte。由于参数值类型和参数值在同一个方括号内，所以我们可以得知它是可选参数。而-After参数又没有单独放在方括号内，所以这就说明该参数不是位置参数。如果要使用该参数，必须输入该参数的名称。

如何使用位置参数？

如上，我们可以执行该命令，其实-LogName位置参数可以省略。

```
Get-EventLog Application
```


 ![](https://img-blog.csdnimg.cn/20210104232025936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

也可以执行如下，Application值会被匹配到 -LogName参数，因为这是第一个位置的参数值，20将表示 -Newest参数值，因为已经指定了该参数

```
Get-EventLog -Newest 20 Application
```


![](https://img-blog.csdnimg.cn/20210104232109505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 参数值

帮助文档提供了每个参数的数据类型，如： 

*   string：字符串
*   string\[\]：字符串，数组，集合，或者是一个列表类型的字符串
*   DateTime：日期
*   Int64、Int、Int32：一个整数类型

![](https://img-blog.csdnimg.cn/20210104234457341.png)

如-ComputerName参数，后面的类型是string\[\]，这意味着这后面可以跟数组、集合，或列表类型的字符串。

```
Get-EventLog application -ComputerName win2008      
powershell会把以逗号为分隔符的列表作为数组值对待      
Get-EventLog application -ComputerName win2008,win7,win2003      
如果某些值包含空格，则该值必须用引号括起来，如果机器名是win 2008      
Get-EventLog application -ComputerName "win 2008"      
当然，没有空格也可以用引号括起来      
Get-EventLog application -ComputerName "win2008","win7","win2003"       
但是不能像下面这样，这样语法是错误的      1
Get-EventLog application -ComputerName "win2008,win7,win2003"
```


我们还可以将机器名放到一个文本我呢间中，一行一个值，如下。然后使用 Get-Content参数读取该文件的内容，然后传给 -ComputerName 参数

```
win2008      
win7      
win2003
```


使用{ }括号表明 { }括号中的先执行。如果小学数学的先执行( )里面的一样 

```
Get-EventLog Application -ComputerName {Get-Content test.txt}
```


 有些参数被称为开关参数，无需输入任何值。开关参数的位置可以随意放置，开关参数总是可选参数。

如 -AsString 参数，该参数后面没有跟参数的类型，并且使用 -full 参数查看的时候，后面有 \[<SwitchParameter>\] ，通过 \[<SwitchParameter>\] 可以确定这个一个开关参数。

![](https://img-blog.csdnimg.cn/2021010423374561.png)

![](https://img-blog.csdnimg.cn/20210104233722806.png)

### 通用参数

输入命令：help  about\_CommonParameters。这个命令是说明公共参数的命令。

通用参数是指可用于任何 cmdlet 的一组 cmdlet 参数cmdlet 开发人员实现，并自动供所有 cmdlet 使用。通用参数可用于任何 cmdlet，但可能不是对所有 cmdlet 都起作用。例如，如果一个 cmdlet 不生成任何详细输出，则 Verbose 通用参数不起任何作用。有几个通用参数可覆盖您使用 Windows PowerShell 首选变量设置的系统默认值或首选项。与首选变量不同，通用参数只影响使用它们的命令。除通用参数外，很多 cmdlet 还提供 WhatIf 和 Confirm 风险缓解参数。给系统或用户数据带来风险的 Cmdlet 通常提供这些参数。

这些通用参数包括：

*   \-Verbose
*    -Debug
*   \-WarningAction
*   \-WarningVariable
*   \-ErrorAction
*   \-ErrorVariable
*   \-OutVariable
*    -OutBuffer

风险缓解参数为：

*   \-WhatIf
*    -Confirm

![](https://img-blog.csdnimg.cn/20210105001754509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### show-command图形化输入参数

假如我们不知道某个cmdlet的参数怎么使用，我们可以使用 show-command cmdlet 来图形化显示该cmdlet的参数的使用。如下，可以看到只有LogName后面有\*，则说明只有LogName是必选参数，其余都是可选参数。

```
Show-Command Get-EventLog
```


![](https://img-blog.csdnimg.cn/20210106112706906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

填入好参数后，我们可以点击运行或者复制。点击运行的话，默认会把命令显示在输入框中。点击复制的话，则会把命令裁切在剪贴板中，需要我们自己去粘贴。

![](https://img-blog.csdnimg.cn/20210106113120769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210106113146369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

实践
--

1：哪一个cmdlet命令能把其他cmdlet命令输出的内容转换为html？

```
Get-Command *html*      
help ConvertTo-Html
```


![](https://img-blog.csdnimg.cn/20210105102039315.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

2：哪一个cmdlet命令可以重定向输出到一个文件(file) 或 打印机 (printer)?

```
Get-Command -noun file,printer      
help Out-File      
help Out-Printer
```


![](https://img-blog.csdnimg.cn/20210105102148343.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210105102225825.png)

3：哪一个cmdlet命令可以操作进程(process)？

```
Get-Command -noun *process*      
help Get-Process
```


![](https://img-blog.csdnimg.cn/20210105102410377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

4：哪一个cmdlet命令向事件日志(eventlog)写入(write)数据？

```
Get-Command -verb write -noun eventlog      
help Write-EventLog
```


![](https://img-blog.csdnimg.cn/20210105102636852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

5：哪一个cmdlet命令可以用于创建、修改或导入别名(alias)?

```
Get-Command *alias*
```


![](https://img-blog.csdnimg.cn/20210105102804330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

6：怎么保证在shell中的输入都在一个脚本（transcript）中，怎么保存这个脚本到一个文本文件中？

```
Get-Command *transcript*
```


![](https://img-blog.csdnimg.cn/20210105103038395.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

7：看看Out-File这个命令的帮助文档。通过该Cmdlet命令输出到文件每一行记录的默认宽度大小为多少个字符？是否有一个参数可以让你修改这个宽度？

```
help Out-File -full
```


![](https://img-blog.csdnimg.cn/2021010510335759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考：《Windows Powershell实战指南》

微软官网powershell文档：[PowerShell 文档 - PowerShell | Microsoft Docs](https://docs.microsoft.com/zh-cn/powershell/ "PowerShell 文档 - PowerShell | Microsoft Docs")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)