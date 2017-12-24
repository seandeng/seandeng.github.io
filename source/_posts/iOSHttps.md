---
title: App Transport Security (ATS) 配置
date: 2016-10-16 10:56:31
tags:
---

App Transport Security（简称ATS）特性, 主要使到原来请求的时候用到的HTTP，都转向TLS1.2协议进行传输。
其实ATS并不单单针对HTTP进行了限制，对HTTPS也有一定的要求：
<!--more-->
> 1. Transport Layer Security协议版本要求TLS1.2以上
> 2. 服务的Ciphers配置要求支持Forward Secrecy等
> 3. 证书签名算法符合ATS要求等

iOS9的出来的时候，apple已经开始要求使用https了，只不过不是强制的。可以通过如下方法继续使用http:

1.在Info.plist中添加NSAppTransportSecurity类型Dictionary。
2.在NSAppTransportSecurity下添加NSAllowsArbitraryLoads类型Boolean,值设为YES

如下图这样

![](http://7xt1bu.com1.z0.glb.clouddn.com/33.png)

这样App Transport Security (ATS)就被禁用了

不过从明年1.1号开始，apple不允许这样做了，那么当 NSAllowsArbitraryLoads 设置为NO或者默认不去设置就会导致所有的http请求失败。

对于应用自己的api都提倡使用https, 对于第三方应用无法修改的可以添加 例外

	<key>NSAppTransportSecurity</key>
	<dict>
	    <key>NSExceptionDomains</key>
	    <dict>
	        <key>duiba.com.cn</key>
			<dict>
				<key>NSThirdPartyExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSIncludesSubdomains</key>
				<true/>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
			</dict>
			<key>clouddn.com</key>
			<dict>
				<key>NSThirdPartyExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSIncludesSubdomains</key>
				<true/>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
			</dict>
			<key>qiniuapi.com</key>
			<dict>
				<key>NSThirdPartyExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSIncludesSubdomains</key>
				<true/>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
			</dict>
	</dict>

NSThirdPartyExceptionAllowsInsecureHTTPLoads 和 NSExceptionAllowsInsecureHTTPLoads 效果一样，只不过前一种表明了是第三方。
这个属性就是用来允许使用http的。

NSIncludesSubdomains 是否包含子域名 比如 xxx.qiniuapi.com 就是 qiniuapi.com 的一个子域名

最后多关注一下第三方sdk对https支持的情况，如果可以使用https就尽量使用https。
即使做好这些，1.1号以后提交审核依然要看苹果爸爸的脸。

附上属性参考列表:
> NSAllowsArbitraryLoads - 设置true即支持所有HTTP请求
> NSExceptionDomains - 添加白名单
> NSExceptionMinimumTLSVersion - 白名单指定域名支持的TLS版本
> NSExceptionRequiresForwardSecrecy - 白名单指定域名是否支持Forward Secrecy
> NSExceptionAllowsInsecureHTTPLoads - 白名单指定域名禁用ATS
> NSThirdPartyExceptionMinimumTLSVersion - 白名单指定第三方服务域名最低支持的TLS版本
> NSThirdPartyExceptionRequiresForwardSecrecy - 白名单指定第三方服务域名是否支持Forward Secrecy
> NSThirdPartyExceptionAllowsInsecureHTTPLoads - 白名单指定第三方域名禁用ATS