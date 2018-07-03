---
layout: post
title: Mac OSX终端走shadowsocks代理
categories: OSX
tags: OSX
---

shadowsocks设置为：

打开shadowsocks
自动代理模式
服务器（香港阿里云）
以zsh作为说明

➜  ~ vim ~/.zshrc  
添加如下代理配置:

# proxy list
alias proxy='export all_proxy=socks5://127.0.0.1:1080'
alias unproxy='unset all_proxy'
:wq保存退出

➜  ~ source ~/.zshrc
使用proxy前先查看下当前的ip地址：

➜  ~ curl ip.cn
当前 IP：112.64.xxx.xx 来自：上海市 联通
或者

~ curl cip.cc
IP	: 140.206.97.42
地址	: 中国  上海

数据二	: 上海市 | 联通

URL	: http://www.cip.cc/140.206.97.42
执行:

➜  ~ proxy
➜  ~ curl ip.cn
当前 IP：47.89.xx.xxx 来自：香港特别行政区 阿里云
如果ip.cn不能用，可以换个类似的站点查询

~ curl cip.cc
IP	: 45.78.47.19
地址	: 美国  加利福尼亚

数据二	: 美国 | 加利福尼亚州洛杉矶市 IT7 Networks

URL	: http://www.cip.cc/45.78.47.19
没问题，终端走了代理，brew update顺畅了- -

如果不需要走代理，执行：

➜  ~ unproxy   
➜  ~ curl ip.cn
当前 IP：112.64.xxx.xx 来自：上海市 联通
proxychains-ng
➜  ~ brew install proxychains-ng
Updating Homebrew...
由于OSX升级后的SIP限制，在proxychains.conf�文件中设置ss的socks5代理，无效了。解决办法是在重启后，在Recovery mode下关闭SIP，但对于强迫症来说，不能忍(安全问题)。详见
rofl0r/proxychains-ng#78

➜  ~ proxychains4 curl ip.cn
[proxychains] config file found: /usr/local/Cellar/proxychains-ng/4.12/etc/proxychains.conf
[proxychains] preloading /usr/local/Cellar/proxychains-ng/4.12/lib/libproxychains4.dylib
当前 IP：112.64.xxx.xx 来自：上海市 联通
配置文件/usr/local/Cellar/proxychains-ng/4.12/etc/proxychains.conf:

 111 [ProxyList]
 112 # add proxy here ...
 113 # meanwile
 114 # defaults set to "tor"
 115 #socks4     127.0.0.1 9050
 116 socks5  127.0.0.1 1080
-- update 2017.07.21 --

osx下使用brew安装google-chrome时：

% brew cask install google-chrome
==> Satisfying dependencies
==> Downloading https://dl.google.com/chrome/mac/stable/GGRO/googlechrome.dmg

curl: (6) Could not resolve host: dl.google.com
Error: Download failed on Cask 'google-chrome' with message: Download failed: https://dl.google.com/chrome/mac/stable/GGRO/googlechrome.dmg
Error: Install incomplete.
通过设置terminal的http代理解决：

% export http_proxy=http://127.0.0.1:1087;export https_proxy=http://127.0.0.1:1087;

原文地址：https://github.com/mrdulin/blog/issues/18