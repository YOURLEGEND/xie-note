![](https://img-blog.csdn.net/20180811103735221?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如图，PC1和PC2处于不同网段，他们到AR3都有两条路可以选，现在要让他们到达AR3[负载均衡](https://so.csdn.net/so/search?q=%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1&spm=1001.2101.3001.7020)，并且当AR1和 AR2的其中一个路由器挂了之后，他们仍然可以正常到达AR3.

他们去往AR3的VRRP的负载均衡容易配置，分别在AR1和AR2上起两个VRRP进程，然后一个进程是主路由，另一个进程是备份路由。上配置,如下

![](https://img-blog.csdn.net/20180811104338836?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180811104401136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180811104436869?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180811104504625?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么最关键的来了，就是AR3回去的路由，要怎么让AR3回去的时候的路由和来的路由是同一条路，即从AR1来的数据还是从AR1回去，从AR2来的数据还是从AR2回去。有很多人可能会说给每个线路设置不同的Cost值，但是这样是行不通的，cost值高的线路数据是肯定不会通过这条线路的(前提是另一条线路没挂)。那么怎么才能让AR3回去的数据也是负载均衡呢？

答案：利用路由策略

从AR1和AR2上先分别把两个网段的路由匹配出来，然后设置基础度量值，也就是基础cost值。最后导入直连路由。

![](https://img-blog.csdn.net/20180811110501289?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180811110834777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)