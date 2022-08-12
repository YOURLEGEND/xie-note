**目录**

[发送文本内容](#t0)

[发送html格式的邮件](#t1) 

[发送带附件的邮件](#t2) 

* * *

发送文本内容
------

```
import smtplib      
from email.mime.text import MIMEText      
from email.utils import formataddr       
mail_user = '123@qq.com'     # 发件人邮箱账号      
mail_pass = 'xxx'            # QQ邮箱的授权码      
received_user = '456@163.com'     # 收件人邮箱账号      
try:      
    msg=MIMEText('邮件内容','plain','utf-8')      1
    msg['Subject']="邮件标题"                           # 邮件的主题，也可以说是标题      1
    msg['From']=formataddr(["发件人名称",mail_user])    # 括号里的对应发件人邮箱昵称、发件人邮箱账号      1
    msg['To']=formataddr(["收件人名称",received_user])   # 括号里的对应收件人邮箱昵称、收件人邮箱账号       1
    #server=smtplib.SMTP("xx.xx", 25)        #发件人邮箱中的SMTP服务器,默认是25      1
    server=smtplib.SMTP_SSL("smtp.qq.com", 465)      #发件人邮箱中的SMTP服务器,这里我写的是QQ邮箱的SMTP服务器，默认是25，SSL端口是465      1
    server.login(mail_user, mail_pass)                 # 括号中对应的是发件人邮箱账号、邮箱密码      1
    server.sendmail(mail_user,[received_user],msg.as_string())  # 括号中对应的是发件人邮箱账号、收件人邮箱账号、发送的邮件      1
    #server.sendmail(mail_user,[received_user,received_user2],msg.as_string())  #多个收件人的话      1
    server.quit()  # 关闭连接      2
    print("邮件发送成功")      2
except Exception as e:      2
    print("邮件发送失败")      2
    print("异常的类型是:%s"%type(e))      2
    print("异常的内容是:%s"%e)
```


![](https://img-blog.csdnimg.cn/20200203131114857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

发送html格式的邮件 
------------

```
import smtplib      
from email.mime.text import MIMEText      
from email.utils import formataddr       
mail_user = '123@qq.com'     # 发件人邮箱账号      
mail_pass = 'xxx'          # 发件人邮箱密码      
received_user = '456@163.com'     # 收件人邮箱账号      
mail_msg = """      
<p>Python 邮件发送测试...</p>      1
<p><a href="http://www.baidu.com">这是一个链接</a></p>      1
"""       1
try:      1
    msg=MIMEText(mail_msg,'html','utf-8')      1
    msg['Subject']="邮件标题"                           # 邮件的主题，也可以说是标题      1
    msg['From']=formataddr(["发件人名称",mail_user])    # 括号里的对应发件人邮箱昵称、发件人邮箱账号      1
    msg['To']=formataddr(["收件人名称",received_user])         # 括号里的对应收件人邮箱昵称、收件人邮箱账号       1
    server=smtplib.SMTP_SSL("smtp.qq.com", 465)  # 发件人邮箱中的SMTP服务器，默认是25，SSL端口是465      2
    server.login(mail_user, mail_pass)                 # 括号中对应的是发件人邮箱账号、邮箱密码      2
    server.sendmail(mail_user,[received_user],msg.as_string())  # 括号中对应的是发件人邮箱账号、收件人邮箱账号、发送的邮件      2
    server.quit()  # 关闭连接      2
    print("邮件发送成功")      2
except Exception as e:      2
    print("邮件发送失败")      2
    print("异常的类型是:%s"%type(e))      2
    print("异常的内容是:%s"%e)
```


![](https://img-blog.csdnimg.cn/20200203132152406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

发送带附件的邮件 
---------

```
import smtplib      
from email.mime.text import MIMEText      
from email.mime.multipart import MIMEMultipart      
from email.mime.application import MIMEApplication       
from email.utils import formataddr       
mail_user = '123@qq.com'     # 发件人邮箱账号      
mail_pass = 'xxx'          # 发件人邮箱密码      
received_user = '456@163.com'     # 收件人邮箱账号      1
try:      1
    message = MIMEMultipart()      1
    message['Subject']="邮件标题"                           # 邮件的主题，也可以说是标题      1
    message['From']=formataddr(["发件人名称",mail_user])    # 括号里的对应发件人邮箱昵称、发件人邮箱账号      1
    message['To']=formataddr(["收件人名称",received_user])         # 括号里的对应收件人邮箱昵称、收件人邮箱账号       1
    message.attach(MIMEText("邮件正文内容","plain","utf-8"))       1
    # 发送方式1,发送当前目录下的test.txt      1
    filename1="test.txt"      2
    att1 = MIMEText(open(filename1, 'rb').read(), 'base64', 'utf-8')      2
    att1["Content-Type"] = 'application/octet-stream'      2
    att1["Content-Disposition"] = "attachment;filename="+filename1  # 这里的filename可以任意写，写什么名字，邮件中显示什么名字      2
    message.attach(att1)       2
    # 发送方式2,发送当前目录下的test.jpg      2
    filename2="test.jpg"      2
    fileApart = MIMEApplication(open(filename2, 'rb').read())      2
    fileApart.add_header('Content-Disposition', 'attachment', filename=filename2)      2
    message.attach(fileApart)       3
    server=smtplib.SMTP_SSL("smtp.qq.com", 465)  # 发件人邮箱中的SMTP服务器，默认是25，SSL端口是465      3
    server.login(mail_user, mail_pass)                 # 括号中对应的是发件人邮箱账号、邮箱密码      3
    server.sendmail(mail_user,[received_user],message.as_string())  # 括号中对应的是发件人邮箱账号、收件人邮箱账号、发送的邮件      3
    server.quit()  # 关闭连接      3
    print("邮件发送成功")      3
except Exception as e:      3
    print("邮件发送失败")      3
    print("异常的类型是:%s"%type(e))      3
    print("异常的内容是:%s"%e)
```


![](https://img-blog.csdnimg.cn/20200203141001802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-135)[基本技能](https://edu.csdn.net/skill/python/python-3-135)[发送邮件](https://edu.csdn.net/skill/python/python-3-135)84711 人正在系统学习中