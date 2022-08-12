数据模型(Data Model)
----------------

数据模型其实就是CobaltStrike官方提供的一些接口，通过这些接口我们得到一些数据。我们所有的数据都保存在服务器上，如[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)器、主机信息、上传下载的东西等。

| Mode | Function | 含义 |
| --- | --- | --- |
| targets | 存储的目标信息 | 显示上线过的主机信息 |
| archives | 显示最近的信息 | 显示最近的输出信息（慎用很卡） |
| beacons | 显示所有的受感染的主机信息 | 显示在线和上线过的主机 |
| credentials | 显示凭据信息 | 我们抓取过的密码信息和制作的票据信息 |
| downloads | 显示下载信息 | 显示我们在受控端下载的信息 |
| keystrokes | 记录键盘输入 | 当我们选择进程记录键盘的时候，会将得到的键盘信息记录下来 |
| screenshots | 屏幕截图显示 | 显示我们截图的二进制信息流 |
| sites | 托管的资产 | 看起来是我们创建的监听的端口个Stager回连的端口 |
| servers |  |  |

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[CobaltStrike 插件编写指南](https://mp.weixin.qq.com/s/i7QzwMAmUyxoBs0CwcGC-g "CobaltStrike 插件编写指南 ")