**目录**

[安装证书服务ADCS](#t0)

[配置ADCS](#t1)

[证书配置](#t2)

* * *

默认情况下，LDAP 通信未加密。这使得恶意用户能够使用网络监控软件查看传输中的数据包。这就是许多企业安全策略通常要求组织加密所有 LDAP 通信的原因。为了减少这种形式的数据泄露，微软提供了一个选项：您可以启用通过安全套接字层 ([SSL](https://so.csdn.net/so/search?q=SSL&spm=1001.2101.3001.7020))/传输层安全性 (TLS) 的 LDAP，也称为 LDAPS。利用 LDAPS，您可以提高整个网络的安全性。

以下实验以Windows server 2012 R2域功能级别为例。

### 安装证书服务ADCS

安装Active Directory证书服务

![](https://img-blog.csdnimg.cn/20210422144155536.png)

勾选第一个证书颁发机构

![](https://img-blog.csdnimg.cn/20210427175627202.png)

然后一直下一步，直到安装完成即可

![](https://img-blog.csdnimg.cn/20210422145406788.png)

### 配置ADCS

![](https://img-blog.csdnimg.cn/20210422145520706.png)

![](https://img-blog.csdnimg.cn/20210422145548906.png)

选择证书颁发机构

![](https://img-blog.csdnimg.cn/20210422145850712.png)

选择企业

![](https://img-blog.csdnimg.cn/20210422145917266.png)

选择根

![](https://img-blog.csdnimg.cn/2021042215000253.png)

创建新的[私钥](https://so.csdn.net/so/search?q=%E7%A7%81%E9%92%A5&spm=1001.2101.3001.7020)

![](https://img-blog.csdnimg.cn/2021042215003013.png)

下一步

![](https://img-blog.csdnimg.cn/20210422150103326.png)

![](https://img-blog.csdnimg.cn/20210422150134226.png)

![](https://img-blog.csdnimg.cn/20210422150158477.png)

![](https://img-blog.csdnimg.cn/20210422150222357.png)

![](https://img-blog.csdnimg.cn/2021042215024945.png)

如下配置完成

![](https://img-blog.csdnimg.cn/20210422150305965.png)

### 证书配置

打开AD CS，选择证书颁发机构

![](https://img-blog.csdnimg.cn/20210422151134843.png)

 选择证书模板，右键管理

![](https://img-blog.csdnimg.cn/20210422151245934.png)

 选择Kerberos身份验证，右键 复制模板

![](https://img-blog.csdnimg.cn/20210422151322651.png)

 然后会有一个Kerberos身份验证的副本，右键更改名称，更改为LDAPS

![](https://img-blog.csdnimg.cn/20210422152739647.png)

 选择LDAPS，右键属性

![](https://img-blog.csdnimg.cn/20210422152812161.png)

 设置模板属性，请求处理——>允许导出私钥(O)

![](https://img-blog.csdnimg.cn/20210422151412757.png)

 创建证书模板

![](https://img-blog.csdnimg.cn/20210422152418337.png)

 选择LDAPS，确定

![](https://img-blog.csdnimg.cn/20210422152855557.png)

![](https://img-blog.csdnimg.cn/20210422152959443.png)

 然后打开控制台，输入MMC

![](https://img-blog.csdnimg.cn/20210422153025615.png)

 添加或删除管理单元

![](https://img-blog.csdnimg.cn/20210422153111672.png)

![](https://img-blog.csdnimg.cn/20210422153228617.png)

![](https://img-blog.csdnimg.cn/20210422153318670.png)

![](https://img-blog.csdnimg.cn/2021042215350669.png)

申请新证书

![](https://img-blog.csdnimg.cn/20210422153540278.png)

然后一直下一步，到了下面选择之前创建的LDAPS

![](https://img-blog.csdnimg.cn/20210422153710432.png)

然后等待注册完成即可！

![](https://img-blog.csdnimg.cn/20210422153801341.png)

 至此，已经完成了LDAPS的配置了。

可以使用ldp.exe来验证，如下：

![](https://img-blog.csdnimg.cn/20210427174815103.png)

![](https://img-blog.csdnimg.cn/20210427174800763.png)

如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)