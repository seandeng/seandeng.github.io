---
title: Hexo NEXT Theme 404
date: 2016-11-19 22:43:38
tags:
---

Hexo使用next主题后 今天发现网站打不开了
vendors目录下文件全部404 
<!--more-->
GitHub Pages 过滤掉了 source/vendors 目录的访问，所以next主题下的source下的vendors目录不能够被访问到，所以就出现了本地hexo s能够正常访问，但是deploy到github就是一片空白，按f12，可以看到大量来自source/vendors的css和js提示404

找到解决方案了。。 
首先修改source/vendors为source/lib，然后修改_config.yml， 将 _internal: vendors修改为_internal:lib 
然后修改next底下所有引用source/vendors路径为source/lib。

1. Hexo\themes\next.bowerrc 
2. Hexo\themes\next.gitignore 
3. Hexo\themes\next.javascript_ignore 
4. Hexo\themes\next\bower.json 

修改完毕后，刷新重新hexo g


参考:

[https://github.com/hexojs/hexo/issues/2238](https://github.com/hexojs/hexo/issues/2238)
