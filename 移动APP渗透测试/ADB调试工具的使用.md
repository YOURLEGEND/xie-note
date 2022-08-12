[ADB](https://so.csdn.net/so/search?q=ADB&spm=1001.2101.3001.7020)(Android Debug Bridge)安卓调试桥，ADB工具是可以方便调试安卓应用的工具。

ADB的安装
------

下载ADB工具，解压，

![](https://img-blog.csdnimg.cn/20190910205956581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将ADB工具的目录加入系统[环境变量](https://so.csdn.net/so/search?q=%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)中，打开CMD窗口，输入adb，看到如下说明ADB工具安装成功

![](https://img-blog.csdnimg.cn/20190910210110239.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ADB连接安卓设备

**ADB连接实体机**

电脑和Android手机连接到同一网络，Android用数据库连接到PC，Android开启USB调试功能(需要先打开开发者选项，然后才能开启USB调试)。

![](https://img-blog.csdnimg.cn/20190910210540121.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

ADB连接，提示目标计算机积极拒绝，无法连接。

```
adb connect 192.168.1.6:5555  #这里是安卓手机的ip地址
```


![](https://img-blog.csdnimg.cn/20190910211038615.png)

这样的话，需要PC下载Android设备型号对应的USB驱动，然后重新连接，这里我嫌麻烦，没有安装。

用ADB连接模拟器，连续点击5次版本号，进入开发者模式

![](https://img-blog.csdnimg.cn/20190910211904831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开USB调试

![](https://img-blog.csdnimg.cn/20190910211948832.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后连接即可。连接成功后，使用命令：adb devices 查看连接的设备

![](https://img-blog.csdnimg.cn/20190910215938263.png)

### ADB命令

```
adb devices       
adb shell      
adb -s emulator-5554 shell   #当有多个设备时，进入指定设备的shell      
adb nodaemon server          #查看是adb bind的是哪个端口        
上传、下载文件，在CMD窗口执行：      
adb push C:\Users\administrator\Desktop\injector /data/local/   #将指定的文件push到安卓设备指定的目录      
adb pull /data/data/com.android.shell C:\Users\administrator\Desktop\   #将包从安卓设备中pull到本地的桌面
```
