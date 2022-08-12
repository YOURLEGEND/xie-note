**目录**

[AK登录](#t0)

[授权码登录](#t1)

[管理Bucket存储桶](#t2)

* * *

ossbrowser是阿里云官方提供的OSS图形化管理工具，提供类似Windows[资源管理器](https://so.csdn.net/so/search?q=%E8%B5%84%E6%BA%90%E7%AE%A1%E7%90%86%E5%99%A8&spm=1001.2101.3001.7020)的功能。

OSS Browser官方版支持 AK(AccessKey)登录 和 临时授权码 登录两种模式。     

### AK登录

 使用AK登录ossbrowser，您可以使用AK(比如子账号AK)登录使用ossbrowser。不推荐使用主账号AK登录。

登录RAM控制台创建子账号，子帐号的权限分为：

*   大权限子账号(即拥有所有Bucket权限，且可以管理RAM配置的子账号)，当前使用的AK已拥有所有Bucket的权限，不需要设置预设OSS路径。
*   小权限子账号(即只拥有部分Bucket或子目录的权限)，当前使用的AK只有某个Bucket或Bucket下某个路径的权限，需要设置预设OSS路径和区域。

记住秘钥：勾选记住秘钥可保存AK秘钥。再次登录时，单击AK历史，可选择该密钥登录，不需要手动输入AK。请不要在临时使用的电脑上勾选。

![](https://img-blog.csdnimg.cn/20210628153232528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 授权码登录

![](https://img-blog.csdnimg.cn/20210628153338456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 管理Bucket存储桶

| 分类 | 配置说明 |
| --- | --- |
| Bucket相关操作 | 
*   创建Bucket
    
    Bucket是您用于存储Object的容器。在上传任何文件到OSS之前，您必须先创建存储空间。有关创建Bucket时如何填写Bucket名称、选择所在地域、ACL权限和存储类型信息，请参见[创建存储空间](https://help.aliyun.com/document_detail/31896.htm#task-bcz-sbz-5db)。
    
*   删除Bucket
    
    如果您不再需要Bucket，请将其删除，以免产生额外费用。有关删除Bucket的注意事项，请参见[删除存储空间](https://help.aliyun.com/document_detail/31897.htm#concept-bcz-sbz-5db)。
    

 |
| Object相关操作 | ossbrowser支持上传、下载、预览、移动或复制文件、生成文件URL等操作。操作过程中有如下注意事项：

*   ossbrowser默认使用分片上传和断点续传上传文件，上传文件最大不能超过48.8 TB。若您因意外中断了文件上传的过程，且未继续完成该文件的上传，则已上传的部分会以碎片（Part）的形式存储在OSS的存储空间（Bucket）中。若您不再需要这些Part，建议您通过以下方式删除，以免产生额外的存储费用。
    
    *   手动删除Part的具体操作，请参见[删除碎片](https://help.aliyun.com/document_detail/31916.htm#concept-r3h-c1y-5db)。
    *   通过生命周期规则自动删除Part的具体操作，请参见[设置生命周期规则](https://help.aliyun.com/document_detail/31904.htm#concept-bmx-p2f-vdb)。
    
    **注意** 如果上传的文件与Bucket中已有的文件重名，则覆盖已有文件。
    
*   移动或复制文件最大不能超过5 GB，5 GB以上文件的移动或复制操作建议使用[ossutil](https://help.aliyun.com/document_detail/50452.htm#concept-cnr-3d4-vdb)。

Object各类操作的其他注意事项，请参见控制台用户指南对应文档。

 |

官方：[https://help.aliyun.com/document\_detail/209974.html?spm=a2c4g.11186623.6.884.2f643d3bNjoSZi](https://help.aliyun.com/document_detail/209974.html?spm=a2c4g.11186623.6.884.2f643d3bNjoSZi)