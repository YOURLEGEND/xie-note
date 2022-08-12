**目录**

[CobaltStrike加载插件](#t0 "CobaltStrike加载插件")

[客户端加载](#t1 "客户端加载")

[服务端加载](#t2 "服务端加载")

[CobaltStrike常见插件](#t3 "CobaltStrike常见插件")

* * *

CobaltStrike加载插件
----------------

CobaltStrike有两种加载插件的方法，一种是在客户端加载，一种是在服务端加载。在客户端加载，当客户端没连接上服务端后，该插件即不会被加载。所以有时候需要在服务端加载某些插件。

### 客户端加载

点击 CobaltStrike——> 脚本管理器

### ![](https://img-blog.csdnimg.cn/20200623210758887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后点击 Load 加载我们的插件，插件后缀格式为 .cna 。

![](https://img-blog.csdnimg.cn/20200623210906253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 服务端加载

CobaltStrike服务器端有个 `agscript` 文件，他是用来在服务器端运行cna插件文件的。

![](https://img-blog.csdnimg.cn/2020062321123042.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200623211324298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
./agscript [host] [port] [user] [pass] </path/to/file.cna>       
[host] #cs服务器的ip地址      
[port] #cs的端口号      
[user] #用户名，用来运行这个脚本的用户名，随便即可。      
[pass] #cs的密码，就是启动cs时你设置的密码。      
[path] #cna文件的路径。
```


![](https://img-blog.csdnimg.cn/20200623212312243.png)

但是我们一般会将其运行在后台

```
nohup  ./agscript  cs的ip  cs的端口  任意用户名  密码  插件路径  &
```


CobaltStrike常见插件
----------------

*   https://github.com/michalkoczwara/aggressor\_scripts\_collection
*   https://github.com/vysecurity/Aggressor-VYSEC
*   https://github.com/killswitch-GUI/CobaltStrike-ToolKit
*   https://github.com/ramen0x3f/AggressorScripts
*   https://github.com/FortyNorthSecurity/AggressorAssessor
*   https://github.com/threatexpress/persistence-aggressor-script
*   https://github.com/threatexpress/aggressor-scripts
*   https://github.com/branthale/CobaltStrikeCNA
*   https://github.com/gaudard/scripts/tree/master/red-team/aggressor
*   https://github.com/001SPARTaN/aggressor\_scripts
*   https://github.com/Und3rf10w/Aggressor-scripts
*   https://github.com/rasta-mouse/Aggressor-Script
*   https://github.com/vysec/Aggressor-VYSEC
*   https://github.com/threatexpress/aggressor-scripts 
*   https://github.com/threatexpress/red-team-scripts 
*   https://github.com/rsmudge/ElevateKit
*   https://github.com/vysec/CVE-2018-4878 
*   https://github.com/harleyQu1nn/AggressorScripts 
*   https://github.com/bluscreenofjeff/AggressorScripts 
*   https://github.com/360-A-Team/CobaltStrike-Toolset 
*   https://github.com/ars3n11/Aggressor-Scripts 
*   https://github.com/michalkoczwara/aggressor\_scripts\_collection 
*   https://github.com/killswitch-GUI/CobaltStrike-ToolKit 
*   https://github.com/ZonkSec/persistence-aggressor-script 
*   https://github.com/rasta-mouse/Aggressor-Script 
*   https://github.com/RhinoSecurityLabs/Aggressor-Scripts 
*   https://github.com/Kevin-Robertson/Inveigh
*   https://github.com/Genetic-Malware/Ebowla 
*   https://github.com/001SPARTaN/aggressor\_scripts 
*   https://github.com/gaudard/scripts/tree/master/red-team/aggressor 
*   https://github.com/branthale/CobaltStrikeCNA 
*   https://github.com/oldb00t/AggressorScripts 
*   https://github.com/p292/Phant0m\_cobaltstrike 
*   https://github.com/p292/DDEAutoCS 
*   https://github.com/secgroundzero/CS-Aggressor-Scripts 
*   https://github.com/skyleronken/Aggressor-Scripts 
*   https://github.com/tevora-threat/aggressor-powerview 
*   https://github.com/tevora-threat/PowerView3-Aggressor 
*   https://github.com/threatexpress/persistence-aggressor-script 
*   https://github.com/FortyNorthSecurity/AggressorAssessor 
*   https://github.com/mdsecactivebreach/CACTUSTORCH 
*   https://github.com/C0axx/AggressorScripts 
*   https://github.com/offsecginger/AggressorScripts 
*   https://github.com/tomsteele/cs-magik 
*   https://github.com/bitsadmin/nopowershell 
*   https://github.com/SpiderLabs/SharpCompile 
*   https://github.com/realoriginal/reflectivepotato

相关文章：[Aggressor Script Tutorial and Reference](https://www.cobaltstrike.com/aggressor-script/index.html "Aggressor Script Tutorial and Reference")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)