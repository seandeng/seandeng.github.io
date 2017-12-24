---
title: hexo 迁移笔记
date: 2017-12-24 21:54:37
tags:
---

hexo部署到 seandeng.github.io 上后 ，如果换了电脑需要重新迁移一遍博客, 因为github上部署的仅仅是hexo生成的静态文件。

## 第一步 上传hexo文件
在 seandeng.github.io 上创建另外一个分支 hexo, master是用来部署生成的网站的
hexo用来存放hexo原文件

<!--more-->

在blog目录中 删掉theme下的git文件 因为它会阻止theme上传git

新建一个文件夹 拷贝blog目录里的文件 除了.git

	git init
	git clone https://github.com/seandeng/seandeng.github.io
	git checkout hexo

依次执行 

	git add . 
	git commit -m ""
	git push origin hexo 

提交网站相关文件


## 第二步 安装 node 和 hexo

在新机器上， 在新的目录中

	git clone https://github.com/seandeng/seandeng.github.io

对于新mac系统来说，不能使用高版本的 node，安装 node 5x 左右即可

	brew install node 安装的是最新版本

怎么安装旧版本呢？用nvm

	brew install nvm 

如果没有.bash_profile文件需要自行touch .bash_profile哦

	$ cd ~ 
	$ vim .bash_profile 

然后添加以下命令：

	export NVM_DIR=~/.nvm
	source $(brew --prefix nvm)/nvm.sh

然后重新source

	$ source .bash_profile

使用nvm安装node

	$ nvm ls-remote 查看 所有的node可用版本
	$ nvm install xxx 下载你想要的版本
	$ nvm use xxx 使用指定版本的node 
	$ nvm alias default xxx 每次启动终端都使用该版本的node 

在这里安装 5.9.0

	nvm install v5.9.0
	nvm use v5.9.0
	nvm alias default v5.9.0

如果之前安装了较新的 hexo可以先删除

	npm uninstall hexo-cli -g #3.0.0版本执行
	npm uninstall hexo -g #之前版本执行

安装 hexo

	npm install hexo -g

进入 blog 目录

	npm install hexo@3.2.0 --save
	npm install
	npm install hexo-deplyer-git

## 第三步

每次写完文章 生成并部署

	hexo g
	hexo d

然后还要推送文件到 hexo 分支上
这样下次在新的机器上只有重复第二步和第三步即可

相关文章列表

[域名申请和dns解析](http://seandeng.com/2016/04/16/name-com-DNSPod/)
[Hexo安装和配置](http://seandeng.com/2016/04/13/hello-world/)
