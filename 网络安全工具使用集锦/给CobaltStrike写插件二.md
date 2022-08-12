**目录**

[定义beacon上线后执行的操作](#t0 "定义beacon上线后执行的操作")

[定义快捷键](#t1 "定义快捷键")

[定义顶层菜单](#t2 "定义顶层菜单 ") 

[定义 alias 关键字](#t3 "定义 alias 关键字")

[注册Beacon命令](#t4 "注册Beacon命令")

[其他命令](#t5 "其他命令")

[输入框的编写](#t6 "输入框的编写")

[EventLog](#t7 "EventLog")

* * *

### 定义beacon上线后执行的操作

```
on beacon_initial {      
    bsleep($1, 0,0);      
    if (beacon_info($1, "os") eq "Windows") {      
        bshell($1, "whoami & hostname & ipconfig /all");      
    }      
}
```


### 定义快捷键

```
bind Ctrl+Q {      
    show_message("hello,word!");      
}
```


然后只要按 Ctrl + Q 就会弹出我们设定的东西

![](https://img-blog.csdnimg.cn/20210126224326411.png)

### 定义顶层菜单 

定义顶层菜单使用popup关键字，其中 item为每一项的定义。记住定义新的顶层菜单需要加一个menubar。

```
popup test {      
    item("&百度一下，你就知道",{url_open("https://xie1997.blog.csdn.net/");});      
}      
menubar("测试", "test");
```


![](https://img-blog.csdnimg.cn/20210126225051731.png)

如果我们并不想创建新的菜单，而是只想在默认的菜单上增加，这样我们popup后面的名字跟个已有的菜单即可。

```
popup help{      
    item("&百度一下，你就知道",{url_open("https://xie1997.blog.csdn.net/");});      
}
```


![](https://img-blog.csdnimg.cn/20210126225342406.png)

### 定义 alias 关键字

可以使用 alias 关键字定义新的Beacon命令，示例如下，其中，blog 函数表示将结果输出到 Beacon 控制台。

```
alias hello {      
    blog($1, "Hello word!");      
}
```


![](https://img-blog.csdnimg.cn/20210127175434836.png)

### 注册Beacon命令

通过 beacon\_command\_register 函数注册 Beacon 命令，示例如下。

```
alis echo {      
    blog($1, "You typed: " . substr($1, 5));      
}      
beacon_command_register(      
    "echo",      
    "echo text to beacon log",      
    "Synopsis: echo [arguments]\n\nLog arguments to the beacon console"      
);
```


### 其他命令

```
在CobaltStrike控制台输出      
blog($1, "hello,word!");      
如果想在函数中执行该命令      
blog($bid, "hello,word!");       
在日志框显示消息      
elog("hello,word!");       
执行操作系统命令      1
bshell($1, "whoami");       1
切换工作目录      1
bcd($bid, "C:\\Windows\\Temp\\test");       1
上传文件      1
bupload($bid, script_resource("/exe/test.exe"));       1
打开一个链接      1
url_open("https://www.baidu.com/");
```


### 输入框的编写

如果我们想要一个输入框，然后让用户填写数据，这个时候可以使用dialg数据模型编写，dialog数据模型有三个参数

*   $1 对话框的名称
*   $2 对话框里面的内容，可以写多个
*   $3 回调函数，用户使用 dbutton\_action 调用的函数

```
popup test {      
	item("&输入框测试",{dialog_test()});      
}      
menubar("测试","test");      
sub dialog_test {      
	$dialog = dialog("这是标题", %(user => "admin",pass => "root"),&show);        
	drow_text($dialog,"user","请输入用户名：");  #用户名输入框      
	drow_text($dialog,"pass","请输入密码：");    #密码输入框      
	dbutton_action($dialog, "确定");   #确定按钮,当点击了该按钮，就会调用回调函数      1
	dialog_show($dialog);    #显示对话框      1
}      1
#回调函数      1
sub show{      1
	println("dialog的引用是: ".$1);      1
	println("按钮是名称是: ".$2);      1
	println("用户名:".$3['user']."密码: ".$3['pass']);      1
}
```


![](https://img-blog.csdnimg.cn/20210127181230279.png)

![](https://img-blog.csdnimg.cn/20210127181241795.png)

![](https://img-blog.csdnimg.cn/20210127181727283.png)

定义 diolog 的时候，会将用户输入的东西传递给第三个参数设置的函数，dialog传递的时候一共会传递三个参数给函数

*   $1 为dialog的引用
*   $2 按钮的名称
*   $3 对话框输入的值

### EventLog

EventLog就是最左侧的框，当有用户连接断开、主机上线下线等操作，都会有日志

![](https://img-blog.csdnimg.cn/20210127182536837.png)

我们可以修改一些

```
#当用户加入cs      
set EVENT_JOIN {      
    show_message($1." 加入到多人战斗中！");      
    return "\cF" . dstamp($2) . " \c3***\c9 $1 \c3 来了！！.";      
}       
#当用户离开cs      
set EVENT_QUIT {      
    return "\cF" . dstamp($2) . " \cA***\cB $1 \cA 离开了.";      1
}      1
#用户在线状态      1
set EVENT_SBAR_LEFT {       1
    return "[" . tstamp(ticks()) . "] " . mynick()." 正在线上！！";      1
}      1
#当主机上线了      1
set EVENT_BEACON_INITIAL {      1
    show_message(dstamp($2)." ".$1." 上线了！");      1
    return "\cF" . dstamp($2) . " \c7*** initial beacon from\c8 $1";      1
}
```


当主机上线了

![](https://img-blog.csdnimg.cn/20210127190834609.png)

当其他用户加入cs中

![](https://img-blog.csdnimg.cn/20210127191128241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[CobaltStrike 插件编写指南](https://mp.weixin.qq.com/s/i7QzwMAmUyxoBs0CwcGC-g "CobaltStrike 插件编写指南 ")