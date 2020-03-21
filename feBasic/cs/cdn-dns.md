## DNS

**DNS 查询的过程**

域名系统DNS(Domain Name System)。

那么问题来了，dns是怎么通过域名来查出ip的呢?我们以浏览器输入www.example.com为例，

1. 检查浏览器缓存
2. 检查操作系统缓存，常见的如hosts文件
3. 检查路由器缓存
4. 如果前几步都没没找到，会向ISP(网络服务提供商)的LDNS服务器查询
5. 如果LDNS服务器没找到，会向跟域名服务器(Root Server)请求解析，分为以下几步：（**从顶级域名开始找**）
   1. 跟服务器返回顶级域名(TLD)服务器如.com，.cn，.org等的地址，全球只有13台，该例子中会返回.com的地址
   2. 接着向TLD发送请求，然后会返回次级域名(SLD)服务器的地址，本例子会返回.example的地址
   3. 接着向SLD域名服务器通过域名查询目标IP，本例子会返回www.example.com的地址
   4. Local DNS Server会缓存结果，并返回给用户，缓存在系统中。

> 如果是用httpDNS的方式，查询会有所不同。
>
> 优点：传统非httpDNS的方式易劫持，绕过LDNS
>
> httpDNS的理解可以参考文章：HttpDns 原理是什么<http://www.linkedkeeper.com/171.html>



更多DNS知识：

<https://juejin.im/entry/5ad011216fb9a028cd457f60>

DNS反射放大：<https://cloud.tencent.com/developer/article/1463080>



## CDN

CDN的全称是Content Delivery Network，即内容分发网络。

典型的CDN系统由下面三个部分组成

- 分发服务系统（cache设备，缓存提供给用户 + 与源站点交互）
- 负载均衡系统（对所有发起服务请求的用户进行访问调度）
- 运营管理系统

使用CDN的方法很简单，只需要修改自己的DNS解析，**设置一个CNAME指向CDN服务商即可**。

> CNAME：Canonical Name，返回另一个域名，令当前查询域名跳去该域名，多个域名->服务器的映射。

### CDN使用过程

用户访问一个未使用CDN的缓存资源的过程为:

1. 浏览器通过前面提到的过程对域名进行解析，以得到此域名对应的IP地址；
2. 浏览器使用所得到的IP地址，向域名的服务主机发出数据访问请求；
3. 服务器向浏览器返回响应数据

使用CDN后：

1. 当用户点击网站页面上的内容URL，经过本地DNS系统解析，DNS系统会最终将域名的解析权交给CNAME指向的CDN专用DNS服务器。
2. CDN的DNS服务器将CDN的全局负载均衡设备IP地址返回用户。
3. 全局负载均衡设备把服务器的IP地址返回给用户。（中间还包含区域负载均衡设备的逻辑）



### CDN的优点

1. 本地Cache加速，加快访问速度
2. 镜像服务，消除运营商之间互联的瓶颈影响，保证不同网络的用户都能得到良好的访问质量
3. 远程加速，自动选择cache服务器
4. 带宽优化，分担网络流量，减轻压力，
5. 集群抗攻击
6. 节约成本



更多CDN知识：

<https://juejin.im/entry/5ad011216fb9a028cd457f60>