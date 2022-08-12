**目录**

[基础SQL语句](#t0)

[增](#t1)

[删](#t2)

[改](#t3)

[查](#t4)

[字符编码](#t5)

* * *

### 基础SQL语句

注释符： #           /\*\*/  
查看所有的数据库：show  databases;  
查看所有的表：       show  tables;

查看user表中的所有列：show columns from user;  
创建数据库：          create  database  test;  
创建数据库并且制定默认字符集： create database db\_student  default  charset utf8;  
创建表：create table t\_student(id int,name char,sex boolean);

*         设置主键：primary key   (该项可以不填)
*         默认值：default         (该项可以不填)
*         自增：auto\_increment   (该项可以不填)
*         不允许为空：not null    (该项不填就允许为空 该项填了就不允许为空)

选择进入数据库:              use  db\_student;  
查看当前所在的数据库:   select  database();  
   
查看创建数据库语句:  show create  database  test;  
查看创建表语句：      show create  table  t\_student;  
查看表结构：             desc   t\_student;     等价于 show columns from t\_student;  
   
外键是数据库一级的一个完整性约束  
添加外键：   
create table  t\_sc(sno char(10) not null，cno char(10) not null，degree decimal(4,1)，primary key(sno,cno)，  
constraint fk\_student\_Sc\_sno foreign key(sno) references t\_student(sno)，  
constraint fk\_course\_sc\_cno foreign key(cno) references t\_course(cno));

###   
增

向表中插入数据： insert  into t\_student   value  (值1，值2，值3);  
自定列插入数据： insert  into t\_student    (列名)     values   (值);  
一次插入多条数据：insert  into  t\_student  values (值1，值2) , (值1，值2 ), (值1，值2);  
增加一列：alter table t\_student add age tinyint after id;

### 删

删除数据库:  drop  database  db\_student;  
删除表：      drop  table  t\_student;  
删除表中的一行： delete  from  t\_student  where  id=1;  
删除一列：  alter  table  t\_student   drop   age;  
  

### 改

修改表中的数据： update  t\_student  set  name='xie'  where  id=2;  
将表中id全部加1：update t\_student set id=id+1;  
修改表名(user—>users)：rename  table  user  to  users;  
修改一列的完整性约束条件：alter  table  t\_student  modify name  varchar(12);  
修改一列的名字(id—>ids)：alter table two change id ids int;

### 查

查询表中的数据  
查询结构：

```
select 列名 from 表名 where 条件1   group by条件2    having条件3  order by条件4   union 运算符   into outfile 输出文件名    limit[m,n]；
```


查询学生表中的所有数据：select \* from t\_student;  
从student选择不重复的id：select distinct id form t\_Student;  
选择前三条数据：select \* from t\_Student limit 3;  
从第二行开始，选择四条数据：select\*from t\_Student limit 1,4;  
获取当前的系统日期：select curdate();  
从学生表中查询姓名和年龄：select sname,year(curdate())-year(sbirth) from t\_student;  
选择name，并且将name命名为名字：select name as  “名字”  form t\_stu;  
将student表中学生的成绩乘以1.2倍：select  degree\*1.2  from t\_student;  
将选择的列创建新的表(查询结果的输出)：create table  t\_new  select id , name from t\_stu;  
写到文件中(查询结果的输出)：select\*from t\_stu  into outfile ‘c:/a.txt’;

查询成绩大于80分的学生学号：select id from t\_Student where degree>80;  
从学生表中查询id在200502-200602的数据：select \* from t\_student where id between 200502 and 200602;  
查询非计算机工程系的学生信息：select\*from t\_student where not sdept=’计算机工程系’;  
查询数学系，计算机科学系学生信息：select\*from t\_student  where sdept  in  (‘数学系’,’计算机科学系’);  
查询除了数学系以外其他系的学生信息：select\*from t\_student where  sdept not in (‘数学系’);  
涉及空值的查询（查询没有成绩的学生信息)：Select\*from t\_student where sdept is  null;

  
**like模糊查询    %:任意(0-n)多个字符     \_：任意一个字符**  
从学生表中查询姓名中含有 勇 的数据：select \* from t\_student where name like  “%勇%”;  
从学生表中查询三个字的姓名，中间的字是勇的数据：select \* from t\_student where name like  “\_勇\_”;  
   
**union联合查询**  
union   重复的数据只显示一个  
union all  显示所有重复的数据  
当 select \* from demo1 union select \* from demo2;  必须得demo1表和demo2表拥有相同的列数才可以。  
当 select id from demo1 union all select username from demo2; 数据显示为1列，重复数据也会显示出来  
   
**总数sum        平均 avg    最高max    最低min**  
查询学生总数：select count(\*) from t\_student;  
查询学生总成绩：select  sum(degree)  from  t\_sc;  
查询学生平均成绩：select  AVG(degree)  from  t\_sc;  
查询学生的最高分和最低分：select MAX(degree) 最高分,MIN(degree)  最低分 from t\_sc;  
查询学号为20050102的学生的总成绩和平均成绩：select SUM(degree), AVG(degree) from t\_sc where id=20050102;  
查询有考试成绩的学生人数：select count(distinct sno) from t\_sc where degree is not null;  
   
**GROUP BY   HAVING**  
查询student各系学生人数：select sdept,count(\*) from t\_student  group by sdept;  
查询student表中男女学生人数：select ssex,count(\*) from student group by ssex;  
查询student表中各系男女学生人数：select sdept,ssex,count(\*) from t\_student group by sdept,ssex;  
查询student表中各系女生人数：select sdept,count(\*) from t\_Student where ssex=’女’ group by sdept;  
或者   select sdept,count(\*) from t\_student group by sdept,ssex having ssex=’女’;  
查询选修了3门以上课程的学生学号：select sno from t\_sc group by sno  having count(\*) >3;  
   
**排序   order by        asc升序    desc降序**  
对student表中的数据按id按升序排列：select \* form t\_student order by id  asc;  
判断student表中有多少列：select \*from t\_student where id=1 order by 5 desc;    如果报错就把5变为4，直到成功

  
**正则表达式**  
查询姓名以“谢”开头的学生信息：select \* from  t\_student  where  name  REGEXP  “ ^谢 ” ;  
查询姓名以“国”结尾的学生信息：select \* from  t\_student  where  name  REGEXP  “ 国$ ” ;  
查询姓名是“谢\*国”格式的学生信息：select \* from  t\_student  where  name  REGEXP  “ 谢.国 ” ;  
   
   
多表连接查询  
交叉连接(得到结果集的行数是两个表的行数的乘积)：select t\_Sc.\*,t\_course.\* from t\_Sc,t\_course; 或者 select \* from t\_Sc,t\_course;  
自连接  
内连接 INNER JOIN(返回两个表的交集)：select \* from t\_sc,t\_course where t\_course.cno=t\_sc.cno;  
或者  select \* from t\_sc  INNER JOIN  t\_course  ON  t\_course.cno=t\_sc.cno;  
外连接  
(1) 左外联LEFT OUTER JOIN     左表为主，右表为辅  
(2) 右外联RIGHT OUTER JOIN   右表为主，左表为辅  
(3) 全外联FULL OUTER JOIN    mysql不支持全外联

### 字符编码

查看字符集：show variables like '%character%';  
查看mysql所支持的字符集：show charset;  
查看表的字符集：show table status from test like '%admin%';  
修改表的字符集：alter table admin convert to character set utf8;  
查看表中所有的字符集：show full columns from admin;  
修改列的字符集：alter table admin modify column password character set utf8;  
   
**修改字符集**  
只对当前连接有效  
设置建立连接使用的编码：set character\_set\_connection=utf8;  
设置数据库的编码：set character\_set\_database=utf8;  
设置结果集的编码：set character\_set\_results=utf8;  
设置数据库服务器的编码：set character\_set\_server=utf8;  
设置数据库客户端的编码：set character\_set\_client=utf8;  
设置文件系统的编码：set character\_set\_filesystem=utf8;  
  
相关文章：[https://www.w3school.com.cn/sql/sql\_syntax.asp](https://www.w3school.com.cn/sql/sql_syntax.asp)

                  [MySQL 详细学习笔记](https://mp.weixin.qq.com/s?__biz=MzU4ODU1MzAyNg==&mid=2247487135&idx=1&sn=df5578a023fd28f5209e58b10bc5ed5c&chksm=fdda40b2caadc9a431c12bfa1aa0017d5fd544d144ccd601ef676fe5218d1d774d9c7fb3a9fc&mpshare=1&scene=2&srcid=0904qBCj2KetPoxglGwLBeNX&sharer_sharetime=1567556120827&sharer_shareid=8626ff039152e52d5a81b31931d01223&from=timeline&clicktime=1567558602&key=deceac43f8ae7820d22d05f319bab9d62a908fcc45c3f5b62b862f91309970a822129420cb2810f1e418bec4c7a4c9c4d1c8a274ab9886fd9ab03ad1d1d24e4daa60c630aed2cdc58594020f7394e634&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=Na%2BE8iw7YWCVWdz1SqD3Q1OUVEIrTdgHyMAStOVu12bkFyPlSb3142KTC3E2XABQ)