---
title: name.com + DNSPod
date: 2016-04-16 02:14:38
tags: 
  - name.com
  - DNSPod
  - 域名解析
---

## 申请域名 

看了下godaddy真是贵 就选择了 name.com
首先查看你的域名是否注册 然后一路next下去付钱就可以了

## 域名解析
域名已经买完了，直接是没法用的，因为没有进行DNS解析，别人是没法正常通过域名访问你的界面的，这里使用[DNSPod](https://www.dnspod.cn/)进行解析.

<!--more-->


去DNSPod注册个账号，添加域名，设置两个A记录。分别是@和w w w，ip地址填上你博客所在服务器的地址比如github提供的ip是192.30.252.153。

![](http://7xt1bu.com2.z0.glb.clouddn.com/2.png)
![](http://7xt1bu.com2.z0.glb.clouddn.com/3.png)

## 设置域名的DNS
这一步是在你申请的域名的服务商提供的网站上做，比如godaddy和name.com。
在相应域名的Csutom DNS里，设置DNS service,添加两条记录f1g1ns1.dnspod.net和f1g1ns2.dnspod.net，。

![](http://7xt1bu.com2.z0.glb.clouddn.com/1.png)


## 漫长的等待

要全球解析生效，得等上一会了，也可以先ping一下自己的设置对不对。

