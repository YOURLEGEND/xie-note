**目录**

[执行powershell命令或powershell脚本](#t0 "执行powershell命令或powershell脚本")

[执行powershell命令](#t1 "执行powershell命令")

[执行powershell脚本](#t2 "执行powershell脚本")

[接收参数执行powershell脚本](#t3 "接收参数执行powershell脚本")

[执行C#程序](#t4 "执行C#程序")

[指定固定参数执行exe](#t5 "指定固定参数执行exe")

[接收一个参数执行exe](#t6 "接收一个参数执行exe")

 [接收多个参数执行exe](#t7 " 接收多个参数执行exe")

[对话框](#t8 "对话框")

[对话框一](#t9 "对话框一")

[对话框二](#t10 "对话框二")

* * *

执行powershell命令或powershell脚本
---------------------------

### 执行powershell命令

```
item "查看powershell执行策略" {      
    blog($1, "-------------------------------------------------------------查看powershell执行策略---------------------------------------------------------");      
    bpowerpick($1,"get-Executionpolicy");      
}
```


![](https://img-blog.csdnimg.cn/20210107173911400.png)

### 执行powershell脚本

```
item "查询RDP端口" {      
    blog($1, "-------------------------------------------------------------查询RDP端口-------------------------------------------------------------");      
    bpowershell_import($1, script_resource("/powershell/RegRdpPort.ps1"));      
    bpowerpick($1,"RegRdpPort");      
    bshell($1,"wevtutil cl \"Windows PowerShell\"");      
}
```


![](https://img-blog.csdnimg.cn/20210107174140828.png)

### 接收参数执行powershell脚本

```
item "查询指定主机当前登录的用户"{      
	blog($1, "-----------------------------------------查询指定主机当前登录的用户-----------------------------------------------");      
	bpowershell_import($1, script_resource("/powershell/PowerView.ps1"));      
	prompt_text("输入指定主机：", "win7", lambda({ bpowerpick(@ids,"Invoke-UserHunter -ComputerName \"$1\"");}, @ids => $1));      
}
```


![](https://img-blog.csdnimg.cn/20210210202145909.png)

执行C#程序
------

### 指定固定参数执行exe

```
item "查看powershell相关信息"{      
    blog($1, "-------------------------------------------------------------查看powershell相关信息--------------------------------------------------------");      
    bexecute_assembly($1, script_resource('/exe/SharpCheckInfo.exe'), "-PowershellInfo");      
}
```


![](https://img-blog.csdnimg.cn/20210107174407818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 接收一个参数执行exe

```
item "查询指定主机当前登录的用户"{      
	blog($1, "-------------------------------------------查询指定主机当前登录的用户-------------------------------------------");      
	prompt_text("输入指定主机：", "win7", lambda({ bexecute_assembly(@ids, script_resource("/exe/PVEFindADUser.exe"),"-current -target $1"); }, @ids => $1));      
}
```


![](https://img-blog.csdnimg.cn/20210210201944511.png)

###  接收多个参数执行exe

```
item "test" {      
    $bid = $1;      
    $dialog = dialog("测试", %(arg1 => "one", arg2 => "two", bid => $bid), &test);      
    dialog_description($dialog, "这是一个测试对话框");      
    drow_text($dialog, "arg1",  "参数一: ");      
    drow_text($dialog, "arg2",  "参数二: ");      
    dbutton_action($dialog, "确定");      
    dialog_show($dialog);      
}      1
sub test {      1
    blog2($bid, "-------------------------------------------------------------test------------------------------------------------------------");      1
    bexecute_assembly($bid, script_resource('/exe/SharpCheckInfo.exe'), "-arg1 ".$3['arg1']." -arg2 ".$3['arg2']);      1
}
```


![](https://img-blog.csdnimg.cn/20210107201905641.png)

![](https://img-blog.csdnimg.cn/20210107201947317.png)

对话框
---

### **对话框一**

```
item "test" {      
    blog($1, "-------------------------------------------------------------执行系统命令-------------------------------------------------------------");      
    prompt_text("输入命令：", "", lambda({ bshell(@ids, "$1"); }, @ids => $1));      
    }
```


![](https://img-blog.csdnimg.cn/20210107203705832.png)

![](https://img-blog.csdnimg.cn/20210107203631410.png)

### **对话框二**

```
item "test" {      
    $bid = $1;      
    $dialog = dialog("测试", %(username => "admin", password => "123456", bid => $bid), &test);      
    dialog_description($dialog, "这是一个测试对话框");      
    drow_text($dialog, "username",  "用户名: ");      
    drow_text($dialog, "password",  "密 码: ");      
    #选择框      
    drow_combobox($dialog, "select", "选择:", @("one","two","three", "WebScan", "four"));      
    #复选框      1
    drow_checkbox($dialog, 'fuxuan', '复选:');      1
    dbutton_action($dialog, "确定");      1
    dialog_show($dialog);      1
}      1
sub test {      1
    blog($bid, "-------------------------------------------------------------test------------------------------------------------------------");      1
    blog($bid, "用户名: ".$3['username']." 密码: ".$3['password']." 选择框：".$3['select']." 复选：".$3['fuxuan']);      1
    if($3['fuxuan'] eq "true"){      1
	blog($bid, "true")      1
    }else{      2
	blog($bid, "false")      2
    }      2
}
```


![](https://img-blog.csdnimg.cn/2021010718462214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210107204329837.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)