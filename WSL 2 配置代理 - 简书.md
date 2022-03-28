# WSL 2 配置代理 - 简书
[![](https://upload.jianshu.io/users/upload_avatars/1851278/934ae8961dfe?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96/format/webp)
](https://www.jianshu.com/u/19ca47345dea)

2021.10.29 10:04:54 字数 731 阅读 1,160

在 Windows 上设置好代理，连上了谷歌开开心心，但是 WSL 2 不能共享 Windows 的代理策略，如果在 WSL 上再装一个代理软件那可太麻烦了，所以得想想办法。

其实办法还挺简单的，可能有的同学不知道，在一个局域网下如果有一台机器配置好了代理，那么这个代理是可以共享给这个局域网下的其他设备的，比较类似软路由哈！

具体方法如下：  
Windows 端:  
这里以 Clash 为例，打开 Allow LAN 选项，如下图所示。如果你使用其他软件，那可能是叫「网关模式」、「允许来自局域网的链接」或者其它的什么，都是一个东西，打开就好了，注意打开这个选项后你的电脑就可以代理整个局域网内的机器了，虽然其他的机器还需要额外的配置，但也还是注意安全。

![](https://upload-images.jianshu.io/upload_images/1851278-ce13a536056f6ed4.png)

image.png

对于 Clash 来说，这个选项是一次性的，下次开机它就关了，不过可以在配置文件里改，通常文件的开头就是。如下图，改成 true 就行。  
[](https://links.jianshu.com/go?to=https%3A%2F%2Fced-md-picture.oss-cn-beijing.aliyuncs.com%2Fimg%2F20210217014312.png)

开启这个选项后，仔细找找，你会找到一个 IP 地址和一个端口号，IP 其实就是本机 IP 啦，这两个数一会有用。

Clash 这个端口 http 和 socks 通用

注意如果后文配置后没有效果，那可能是 Windows 防火墙的锅，快去配置防火墙放行 Clash

WSL 2 端:  
说是 WSL 2，其实其他的手机电脑都能连上，就在网络设置或者 WiFi 设置那有个配置代理，把上面得到的 IP 和端口填上就行。

下面就说说在 WSL 2 下怎么操作吧！

    ## 获取主机 IP
    ## 主机 IP 保存在 /etc/resolv.conf 中
    export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*') 

> Q: 以上似乎会定位到默认网关 `192.168.3.1`  
> A: 切换到 WSL2

    export https_proxy="http://${hostip}:7890";
    export http_proxy="http://${hostip}:7890"; 

注意修改成你的端口

如果是 socket5 协议的话

    export http_proxy="socks5://${hostip}:7890"
    export https_proxy="socks5://${hostip}:7890" 

如果端口一样就可以合并成一句话，http 的同理

    export all_proxy="socks5://${hostip}:7890" 

使用 `curl` 即可验证是否代理成功，如下有返回值说明成功

    ➜  ~curl google.com
    <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8"><TITLE>301 Moved</TITLE></HEAD><BODY><H1>301 Moved</H1>The document has moved<A HREF="http://www.google.com/">here</A>.</BODY></HTML> 

可以将上面命令选择你需要的添加到 `.bashrc` ，这样会让代理一直开启。  
使用 zsh 应该保存到 `~/.zshrc`

更新配置

或者添加如下，需要代理的时候输入 `setss` 即可设置代理，取消代理就 `unsetss` ，或者新开一个窗口。  
下面第二条的长命令你好像得根据情况删掉一部分。

    export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
    alias setss='export https_proxy="http://${hostip}:7890";export http_proxy="http://${hostip}:7890";export all_proxy="socks5://${hostip}:7890";'
    alias unsetss='unset all_proxy' 

如下是我在 `~/.zshrc` 中添加的配置文件

    export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
    alias setss='export all_proxy="socks5://${hostip}:7890";'
    alias unsetss='unset all_proxy' 

验证：

    ➜  ~setss
    ➜  ~curl google.com
    <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8"><TITLE>301 Moved</TITLE></HEAD><BODY><H1>301 Moved</H1>The document has moved<A HREF="http://www.google.com/">here</A>.</BODY></HTML>➜  ~cedar unsetss➜  ~cedar curl google.comcurl: (28) Failed to connect to google.com port 80: Connection timed out 

\|

更多精彩内容，就在简书 APP

"小礼物走一走，来简书关注我"

还没有人赞赏，支持一下

[![](https://upload.jianshu.io/users/upload_avatars/1851278/934ae8961dfe?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100/format/webp)
](https://www.jianshu.com/u/19ca47345dea)

总资产 100 共写了 10.7W 字获得 116 个赞共 58 个粉丝

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

-   在 WSL2 环境中 clone 一个很大的 git 项目，不走代理速度很慢，所以研究了一下怎么让 WSL2 走 ...

-   获取 WSL2 IP 地址 在 WSL2 中，要访问 Windows 上运行的网络应用（例如在 Windows 上...

    [![](https://upload-images.jianshu.io/upload_images/25093595-f6202105285ec9fe.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240/format/webp)
    ](https://www.jianshu.com/p/0403797c13b6)

-   准备工作 Windows 10 需要保证 Windows 10 至少为 Windows 10 Fall Creat...

    [![](https://upload.jianshu.io/users/upload_avatars/10494460/e253aee0-3418-49be-8ad2-e489af8a7c83.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48/format/webp)
    叫我夹子](https://www.jianshu.com/u/32aa9f2d2aba)阅读 728 评论 0 赞 1

-   缘起 突然的想法, 搜索关键词 Markdown + 思维导图得到了 MrDoc 项目\[[https://gitee.com/..](https://gitee.com/..).

    [![](https://cdn2.jianshu.io/assets/default_avatar/5-33d2da32c552b8be9a0548c7a4576607.jpg)
    粗识名姓](https://www.jianshu.com/u/da32eb2a78e6)阅读 328 评论 0 赞 0

    [![](https://upload-images.jianshu.io/upload_images/3071283-4fe6de2e4cec8298.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240/format/webp)
    ](https://www.jianshu.com/p/5208ee84e449)

-   一. 安装 windows10 2004 版 建议全新安装 windows10 2004，升级到 2004 版时开机启动项在...

-   今天青石的票圈出镜率最高的，莫过于张艺谋的新片终于定档了。 一张满溢着水墨风的海报一次次的出现在票圈里，也就是老谋...

    [![](https://cdn2.jianshu.io/assets/default_avatar/2-9636b13945b9ccf345bc98d0d81074eb.jpg)
    青石电影](https://www.jianshu.com/u/aa52975c0a31)阅读 3,977 评论 0 赞 2

    [![](https://upload-images.jianshu.io/upload_images/680837-5490380964d6549d?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240/format/webp)
    ](https://www.jianshu.com/p/aed76156f092)

-   一、jQuery 简介 JQ 是 JS 的一个优秀的库，大型开发必备。在此，我想说的是，JQ 里面很多函数使用和 JS 类似，所...

-   字符串 1. 什么是字符串 使用单引号或者双引号括起来的字符集就是字符串。 引号中单独的符号、数字、字母等叫字符。 ...

-   一场说走就走的旅行。 简单地整理几件必备旅行用品，7.18 日早上 8.00 准时出发了，计划一路南下然后绕西南一圈回恩...

    [![](https://upload.jianshu.io/users/upload_avatars/3167453/9ee740ca4fe9.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48/format/webp)
    悠游鱼](https://www.jianshu.com/u/8340b230e41f)阅读 2,212 评论 2 赞 6

    [![](https://upload-images.jianshu.io/upload_images/3167453-9250facdfdb94612.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240/format/webp)
    ](https://www.jianshu.com/p/6b1e1eb1c05c)

-   原来看过一段话，第一厉害的人有能力没脾气，第二厉害的人有能力有脾气，最差的是没能力有脾气的人。 以我最...
       [![](https://cdn2.jianshu.io/assets/default_avatar/12-aeeea4bedf10f2a12c0d50d626951489.jpg)
       凉风豆豆](https://www.jianshu.com/u/3f9caec80436)阅读 825 评论 2 赞 0 
    [https://www.jianshu.com/p/af6a02f98d5a](https://www.jianshu.com/p/af6a02f98d5a)
