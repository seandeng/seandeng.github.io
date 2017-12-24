---
title: Hexo安装和配置
tags: Hexo
---

看着同事们blog的好漂亮，自己也搭一个吧。

整个过程就是如果你在一台vps上搭，很容易碰到各种坑，各种版本不适配。

到mac下就感觉好很多，苹果大法好。

<!--more-->

## hexo介绍
hexo是一个基于hexo是一款基于Node.js的静态博客框架。

速度快
支持markdown语法
支持命令行的部署到 github pages， heroku 或者其它站点
丰富的插件支持
好看的主题

这是hoxo的官网[https://hexo.io/](https://hexo.io/)


## Git安装和设置



	brew install git          #Mac电脑使用brew安装 


然后设置好git账户
使用Github Page搭建博客, 需要在github建立仓库,仓库名为username.github.io

## Node.js安装


	brew install node  #最新版的node.js的包中已经集成了npm包管理工具


使用以下命令验证是否安装成功


	node -v
	npm -v


## Hexo安装与设置
Node, npm和Git都安装成功, 开始安装hexo


	npm install hexo -g  #-g表示全局安装, npm默认为当前项目安装




如果遇到报错

	{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }


则用下列语句安装

	npm install hexo --no-optional


Hexo使用命令:


	hexo init blog  #执行init命令初始化到你指定的hexo目录
	cd blog
	npm install    #install before start blogging
	hexo generate       #自动根据当前目录下文件,生成静态网页
	hexo server         #运行本地服务


浏览器输入[http://localhost:4000](http://localhost:4000)就可以看到效果。

如果不想博文在首页全部显示, 并能出现阅读全文按钮效果, 需要在你想在首页显示的部分下添加


	<!--more-->

## 添加博文
	hexo new "postName"  #新建博文,其中postName是博文题目

博文会自动生成在博客目录下source/_posts/postName.md

打开sublime 用markdown的方式去编辑blog吧

最后

	hexo g  ＃生成
	hexo s  ＃部署

在访问一次就能看到你刚才的博文了[http://localhost:4000/](http://localhost:4000/)


## 主题更改


默认的主题一般，换个喜欢的主题吧：
Hexo[主题列表](https://github.com/hexojs/hexo/wiki/Themes)
自己用的这个，貌似使用人数是最多的，因为简介美观。
[iissnan/hexo-theme-next](https://github.com/iissnan/hexo-theme-next)


	cd blog
	git clone https://github.com/iissnan/hexo-theme-next.git themes/next


查看下效果

	hexo g   #重新生成静态网页
	hexo s   #服务器启动

## 部署到Github

将博客部署到github上 打开./_config.yml

	# Deployment
	## Docs: https://hexo.io/docs/deployment.html
	deploy:
	  type: git
	  repo: https://github.com/seandeng/seandeng.github.io.git
  	  branch: master

在配置文件中设置号部署设置后 打命令

	hexo deploy  #进行部署

以后直接可以在本地写好blog 打下面的命令 就上传成功了

    hexo g -d

    INFO  Start processing
	INFO  Files loaded in 798 ms
	INFO  Generated: 2016/04/10/hello-world/index.html
	INFO  1 files generated in 260 ms
	INFO  Deploying: git
	INFO  Clearing .deploy_git folder...
	INFO  Copying files from public folder...
	[master 8d87c9c] Site updated: 2016-04-16 01:57:06
	 1 file changed, 12 insertions(+), 1 deletion(-)
	To https://github.com/seandeng/seandeng.github.io.git
	   e3bd1ce..8d87c9c  HEAD -> master
	Branch master set up to track remote branch master from https://github.com/seandeng/seandeng.github.io.git.
	INFO  Deploy done: git





