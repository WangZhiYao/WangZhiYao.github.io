---
title: HPE Proliant Microserver Gen10 Plus
date: 2020-04-10 22:21:43
categories: 
 - Microserver Gen 10 Plus
tags: 
 - Life
---

> Update：再通过折腾发现，的确是因为我的显示器 DP 版本过高所以一开始视频没有输出。

> Update：后续折腾发现，DP 口没有输出有可能是因为我的显示器 DP 口为 1.2 而 Gen10 Plus 自带的 DP 口为 1.0 导致的，而且，在通过 VGA-HDMI 的转换头之后再通过 HDMI 线连接到我显示器的 HDMI 口依旧没有输出，但是我同时将 DP 口 也插上线连接到我显示器之后 VGA 口就有输出了(Round 3 那会儿能点亮是我阴差阳错把 DP 口也一起插上了)，这真是人间迷惑行为。但是一切都有可能是我显示器的原因，因为我没有多的屏幕，所以不好确定到底是什么原因。

> 配置：
>
> * 型号：P16006-001
> * CPU：Xeon E-2224
> * 内存：16GB ECC
> * 不带硬盘
> * 不带 Smart Array 100i Raid 卡
> * 不带 iLO Enablement Kit
> * 只有一个 PCI-E 接口！只有一个 PCI-E 接口！只有一个 PCI-E 接口！

<!-- more -->

再是花费：￥4597.77 ($655.94) + ￥647.40 (转运) + ￥599.70 (13%关税) = ￥5844.87

购买过程：

1. 3月26日从美亚购买的 Gen10 Plus，一开始在美亚买的时候是639刀左右，然后一觉醒来订单被取消了，说没有库存了，然后再一搜，上了个655刀的，这个操作有点迷惑，但是想了很久了，没办法，赶紧再下单，发的UPS
2. 3月28日到达顺丰美国转运仓，然后走的顺丰海淘转运，入库时称重为7.25kg
3. 4月1日顺丰收取快件，疫情原因加上周末美国那边不上班，所以拖了很久
4. 4月2日开始转运到纽约
5. 4月5日到达香港，然后清明节海关放假
6. 4月7日到达广州，进行清关，由于顺丰海淘是阳光清关，主动申报，所以必被税
7. 4月8日清关完成
8. 4月9日到达广州白云机场，准备国内派送
9. 4月10日签收

## Round 1

![签收](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_191257.webp)

![开箱](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_193534.webp)

![开箱](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_193551.webp)

![开箱](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_193702.webp)

![开箱](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_193742.webp)

![开箱](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_194406.webp)

![注意美版的电源插头](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_194440.webp)

![机箱内部-侧面](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_195443.webp)

![机箱内部-正面](/images/articles/hpe-proliant-microserver-gen10-plus/20200410_200213.webp)

同时我还买了一块三星 860EVO 250G 用来做系统盘 和东芝 P300 2T 用来存数据之类的：

![Samsung 860 EVO 250G 与 东芝P300 2T](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_014937.webp)

装上之后发现，2.5寸的三星SSD插在1号硬盘位也就是左上角完全是靠接口卡住的，而我又有强迫症，这样不是长久之计，请注意由于硬盘接口在左边，所以普通的硬盘托架是没有用的，因为那玩意是把硬盘固定在中间，实际上要在硬盘架的左边：

![4个硬盘位](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_014624.webp)

然后我就又买了一个2.5寸转3.5寸的托架，同时这个电源插口是美国标准，于是又买了个电源插头转换器：

![硬盘支架与电源插头转换器](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_012429.webp)

好在这个电源跟以前的笔记本电源是一样的，分为插头跟变压器，刚好室友的笔记本也是这样的，所以就借了室友的笔记本电源插头来使用一下，然后我又翻出来一根DP线，跟我的显示器连接起来，然后沙雕的事情就发生了：

**DP口没有输出**

**DP口没有输出**

**DP口没有输出**

然后我又买了根 HDMI 转 VGA 的线：

![实际上买错了，不应该是 HDMI 转 VGA，应该是 VGA 转 HDMI 转换头](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_020715.webp)

从说明书中可以得知 Gen10 Plus 中 CPU 为 E-2224 的这款没有核显，同时自带的 DP 口为1.0：

![Gen10 电子说明书](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_021301.webp)

DP1.0版本的输出1080P都悬，更何况我打算在上面装 Plex 当 HTPC，所以我又下单买了一块 GT 1030 亮机卡（其实现在想起来完全没必要，我用不上硬解）：

![技嘉 GT 1030](/images/articles/hpe-proliant-microserver-gen10-plus/20200411_012503.webp)

所以目前状况就卡在了机都没点亮的状态下...

等后续快递都到了再继续折腾

## Round 2

今天显卡到了，GT 1030 半高亮机卡，拆开后盖，取下主板上的PCI-E扩展板，将这个半高显卡插进去再装回到主板上：

![这个半高显卡的散热鳍片真的很锋利](/images/articles/hpe-proliant-microserver-gen10-plus/20200412_005528.webp)

![将显卡插入 PCI-E 槽](/images/articles/hpe-proliant-microserver-gen10-plus/20200412_005634.webp)

![装完的样子](/images/articles/hpe-proliant-microserver-gen10-plus/20200412_005756.webp)

![机箱背面](/images/articles/hpe-proliant-microserver-gen10-plus/20200412_010202.webp)

关于这个 PCI-E 槽可以有很多选择：万兆网卡，启动盘等等，具体可以看下面这个视频，里面介绍了这个 PCI-E 口可以怎么玩，以及在合理的情况下 CPU 可以更换成什么：

<iframe width="560" height="315" src="https://www.youtube.com/embed/x-_CyKuJz9s" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

所以这个 PCI-E 槽最终用来做什么还是看个人喜好 ，Gen10 Plus 的 E2224 版本是没有核显的，而我打算装个 Plex 来管理我的电影，所以需要一张独立显卡用来解码视频等，于是我就上了一张显卡。

在显卡装上去之后，我用了一根 HDMI 线来连接到我的显示器，然后开机，终于点亮了，但是又遇到一个很沙雕的问题：当我按下 F10 准备在系统自检之后进入 Intelligent Provisioning 用官方推荐的方式来安装系统时，画面一黑提示：no such device : EMBED340。

百思不得其解，到底是哪里出了问题，又去看了下文档，下了个 Intelligent Provisioning 的升级包(SSP)放在U盘里，准备升级下 Intelligent Provisioning 试试，因为我看到 F9 的话能进到 BIOS 设置，里面有从媒体选择升级包的选项，然后把U盘插到前面的 USB 口，又沙雕了，进去发现媒体列表里根本没有U盘这个东西.

既然官方的路子走不通，我就只能自己再用 Rufus 做了个 Win10 的启动盘，想着既然有了视频输出，系统总该能装上了，然后又是这种沙雕问题，画面卡在了 lanuch efi.ini 大概是这个，不太记得了，反正是加载 Win10 安装文件的过程，又是百思不得其解.

直接安装不行，我用 PE 装总可以吧？然后我又给U盘装了个 WePE ，拖了个 WinServer 2019 的 iso 进去，然后重启，从U盘启动，当我从U盘启动之后，WePE 有个选分辨率的界面，我选择之后，又沙雕了，屏幕黑了，但是从我的显示器指示灯来看，这个 HDMI 口是有输出的，但是就是一片黑，等了很久还是黑的，这就很迷惑了.

就目前为止两个让我迷惑的点：1.主板上自带的DP口到底是做什么用的，2.我上了独显之后为什么离开 BIOS 屏幕就黑了但是有视频输出

没办法，没有 VGA 的线，一切只有等明天 VGA 的线到了再看到底是怎么回事.

## Round 3

由于之前买错了，买了根 HDMI 转 VGA 线，用不了，实际上应该买的是 VGA-HDMI 的转接头，所以前面买的线换货耽误了一天，刚好与硬盘托架一起到了：

![应该是用 VGA 转 HDMI 转接器](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_234052.webp)

![使用硬盘托架固定 SSD 之后](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_201633.webp)

然后使用 VGA-HDMI 转接头，连接 HDMI 线到我的显示器，然后开机，终于，亮了，然后进入 Intelligent Provisioning , 啊 ！！！

![成功亮机](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_202836.webp)

然后就是很沙雕的问题，不知道是网的原因还是什么，反正一直装系统失败，然后我就去 HPE 官网下了个定制的 VMware ESXI 放到U盘然后插到前面的 USB 口，通过 Intelligent Provisioning 使用 U 盘安装，果然一下就 OK 了.

![](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_221016.webp)

![使用 Intelligent Provisioning 安装 VMware ESXI](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_205355.webp)

![VMware ESXI 安装中](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_220744.webp)

![VMware ESXI 安装完毕](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_224954.webp)

VMware ESXI 安装完成.

在安装的过程中需要输入密码，密码必须包含大小写数字与符号，其中符号这个用 `.` 通过不了，但是用 `!` 就可以，这个很迷...

![VMware ESXI 后台](/images/articles/hpe-proliant-microserver-gen10-plus/20200413_232214.webp)

暂时折腾完毕。