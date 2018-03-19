---
title: 如何提高用户下载大文件的成功率
tags:
  - 下载器
  - 在线安装
categories: 其他
date: 2018-02-28 19:38:34
---



这里总结了一些文件下载时比较常见的问题，以及相应的解决 

<!-- more -->

如何定义“下载成功”呢，简单来说，如果用户成功地把服务器中的安装包下载到本地，那么我们就认为本次下载成功。以下情况表示下载失败：

- 下载过程中发生任何错误，导致下载中断
- 下载时间过长，用户主动放弃下载
- 下载到本地的文件与服务器的文件不匹配(数据传输过程中遭到篡改)

上面任何一种状况出现，都会导致用户在下载阶段流失。现实中的网络远比我们想象的更复杂，也更脆弱。因此，要彻底杜绝用户在下载阶段流失几乎是不可能的，我们只能尽可能去接近这个目标。

# 问题汇总
我们先简单介绍一下，操作系统是如何处理一个文件的下载请求的：
1. 客户端发起下载文件的请求，我们假定下载的连接为`http://test.com/a.txt`
2. 操作系统拿到这个请求，首先调用Local DNS服务，获取域名`test.com`对应的IP地址
3. 拿到IP地址后，操作系统底层将请求封装为HTTP报文，发送到指定的IP地址
4. `text.com`域名对应地址的服务端收到HTTP请求后，从硬盘中读取具体的文件，并将内容发送给客户端
5. 客户端从收到返回的数据后依次写入本地文件，由于带宽和文件的限制，这个过程可能会持续很长时间。

在这个流程中，最有可能出现的问题包括：
- 操作系统在获取`test.com`对应的IP地址时失败，可能有如下原因：
	- 用户与DNS之间的网络通信不稳定，导致访问超时
	- DNS服务器找不到指定域名或者在寻找域名时超时，如果下载地址是一个不常见的域名，出现该问题的概率会变高
	- 遭遇DNS劫持，DNS服务基于UDP协议，这让其很容易遭到劫持，产生非预期的结果。
- 操作系统在给指定IP发送HTTP报文时失败，可能的原因如下：
	- HTTP连接超时。取决于用户网络状况。
- 服务端给客户度发送数据时，可能出现以下问题：
	- 网络断开，除了用户本身网络断开，用户和下载站点的链路也有可能断开。
	- [HTTP劫持](https://yq.aliyun.com/articles/2666?spm=a3c0i.o27118zh.a3.1.64163e67KfYc5z )。事实上HTTP劫持比DNS劫持更常见，运营商和路由器都有可能对你的HTTP数据进行注入操作。

关于DNS劫持和HTTP劫持可以参考: [这篇文章](http://bigsec.com/bigsec-news/wechat-16824-yunyingshangjiechi)。

# 解决方案
如何降低用户下载大文件的失败率呢，可以考虑以下几种方案。

## 使用CDN部署文件
我们可以自己购买机器部署多个下载节点，不过这将耗费大量的资源。使用现成的CDN服务可以将你的文件缓存到各个CDN节点上，可以避开单台服务器的负载和带宽限制。除此之外，因为CDN节点数量众多，使得不同地区，不同运营商的用户都能有较好的访问效果。

## 使用HTTPDNS
如果你使用多台服务器或者CDN来提供文件下载服务，那么最好使用HTTPDNS来代替默认的LocalDNS。因为：

- LocalDNS使用UDP协议，很容易导致DNS劫持，而基于HTTP协议的HTTPDNS则不那么容易遭到劫持。
- HTTPDNS基于HTTP协议，因此可以获取到用户的IP，HTTPDNS服务商就可以根据用于IP来分配更合适的CDN节点，相比于LocalDNS准确率更高。

## 使用HTTPS传输文件
使用HTTPS的主要目的是防止HTTP劫持。实际环境下，遇到HTTP劫持的概率并不算低。而且一旦遇到这样的问题，用户会有很大概率直接放弃下载。通过伪造URL确实可以避开一部分劫持，但是一劳永逸的方法是使用HTTPS

## 使用专用下载器
无论我们怎么预防，都不能保证100%不发生错误，因此使用专用下载器最大的一个好处就是：隐藏错误细节，自动重试，避免消耗用户的耐心(尤其是对计算机网络不了解的电脑用户)。

大部分情况下，市面上的下载软件都可以做到上述的功能。使用到下载器的应用一般是游戏，或者一些需要在线安装和扩展的软件(例如Android SDK Manager, VS Installer等 )。下载超时？下载文件错误？这些都没关系，下载器会自动帮你重新下载，用户甚至都不会知道曾经发生了错误。