IPV6隧道
------

IPv6(Internet Protocol Version 6)，是用来替代IPv4的一种新的IP协议。IPv6隧道技术是指通过IPv4隧道传送IPv6数据报文的技术。为了在IPv4海洋中传递IPv6信息，可以将IPv4作为隧道载体，将IPv6报文整体封装在IPv4数据报文中，使IPv6报文整体封装在IPv4数据报文中，使IPv6报文能够穿过IPv4海洋，到达另一个IPv6小岛。

**IPv6隧道的工作过程如下：**

1.  节点A要向节点B发送IPv6报文，首先需要在节点A和节点B之间建立一条隧道
2.  节点A将IPv6报文封装在以节点B的IPv4地址为目的地址，以自己的IPv4地址为源地址的IPv4报文中，并发往IPv4海洋
3.  在IPv4海洋中，这个报文和普通IPv4报文一样，经过IPv4的转发到达节点B
4.  节点B收到此报文之后，解除IPv4封装，取出其中的IPv6报文

攻击者有时会通过恶意软件来配置允许进行IPv6通信 设备，以避开防火墙和入侵检测系统。有一点需要指出：即使设备支持IPv6，也可能无法正确分析封装了IPv6报文的IPv4数据包

配置隧道和自动隧道的主要区别是：只有在执行隧道功能的节点的IPv6地址是IPv4兼容地址时，自动隧道才是可行的。在为执行隧道功能的节点分配IP地址时，如果采用的是自动隧道方法，就不需要进行配置。

配置隧道方法则要求隧道末端节点使用其他机制来获得其IPv4地址，例如采用DHCP、人工配置或其他IPv4的配置机制。

支持IPv6的隧道工具有socat、6tunnel、nt6tunnel等。

未完待续。。