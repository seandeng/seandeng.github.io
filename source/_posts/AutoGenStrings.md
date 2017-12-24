---
title: storyboard 和 xib文件的国际化
date: 2016-10-30 09:28:53
tags: 国际化
---

iOS国际化

storyboard 和 xib文件的国际化
<!--more-->
每种语言定制一套Storyboard会造成以后需求变更的时候需要同时修改其它语言，比较麻烦。
所以采用的是 基于基础的Base StoryBoard以及每种语言一套strings

![](http://7xt1bu.com1.z0.glb.clouddn.com/36.png)

xib也是一样

![](http://7xt1bu.com1.z0.glb.clouddn.com/37.png)

不过，相应语言的strings一旦生成后，Base Storyboard或者 Base Xib有任何编辑都不会影响到strings，这就意味着如果我们删除或添加了一个UILabel的text，strings也不能同步改动。

实际上，storyboard的翻译是由ibtool工具生成的，你可以用terminal进入storyboard所在的目录，输入下面指令，就可以得到一个新的翻译文本

>ibtool Main.storyboard --generate-strings-file ./NewTemp.string

但是ibtool生成的strings文件是BaseStoryboard的strings(默认语言的strings)，且会把我们原来的strings替换掉。所以我们要做的就是把新生成的strings与旧的strings进行冲突处理(新的附加上，删除掉的注释掉)，这一切可以用这个python脚本来实现，见[AutoGenStrings.py](https://raw.githubusercontent.com/mokai/iOS-i18n/master/i18n/RunScript/AutoGenStrings.py)。然后我们将借助Xcode 中 Run Script来运行这段脚本。这样每次Build时都会保证语言strings与Base Storyboard保持一致。

图中有两个脚本的目的是 storyboard和xib都需要生成一套strings。 需要生成xib的可以修改一下AutoGenStrings.py里的文件类型。

![](http://7xt1bu.com1.z0.glb.clouddn.com/38.png)

![](http://7xt1bu.com1.z0.glb.clouddn.com/39.png)

strings 使用 key 和 map的格式

![](http://7xt1bu.com1.z0.glb.clouddn.com/40.png)

这样再配合上Localizable.strings就基本可以做好整个项目的国际化了。还是比较方便的。

下次有空了再实践下应用内直接切换语言的办法。

参考:

[通过脚本使storyboard翻译自增](http://www.cnblogs.com/levilinxi/p/4296712.html)

