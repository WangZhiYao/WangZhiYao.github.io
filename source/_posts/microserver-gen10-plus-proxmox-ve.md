---
title: Microserver Gen10 Plus 安装 Proxmox VE
date: 2020-04-19 23:39:14
toc: true
categories: 
 - Microserver Gen 10 Plus
tags: 
 - Proxmox VE
---

## 1.下载镜像

首先是去 Proxmox VE 的 [官网](https://www.proxmox.com/en/downloads/category/iso-images-pve) 下载镜像

![Proxmox VE 官网](/images/articles/microserver-gen10-plus-proxmox-ve/official-website.webp)

<!-- more -->

## 2.制作启动盘

使用 Rufus 或者 UltraISO 或者 balenaEtcher 都行，将镜像刷进 U盘 内，制作成启动盘。

## 3.安装

建议将 U盘 插在 Gen10 Plus 前面板的 USB 口上，这样速度会快一点（由于没有买 iLO Enablement Kit，所以我把机器搬到桌子上连到显示器上，全程只能拍屏幕），接着启动电源，等到进入这个界面的时候按 F11，到启动菜单，选择你的 U盘 启动：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211154.webp)

然后就进入 Proxmox VE 的安装界面，选择第一个 **Install Proxmox VE** ：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_214324.webp)

点击右下角的 **I agree** :

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211307.webp)

然后选择要安装到哪个硬盘，在这里可以点击旁边的 **Options** 按钮设置成 zfs 做软 raid ：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211320.webp)

打开 **Options**，里面默认是 **ext4 **:

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211340.webp)

可以点击下拉菜单选择其他：

> ext3, ext4, xfs, raid 0, raid 1, raid 10, raidz 1, raidz 2, raidz 3

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211359.webp)

再接下来就是填写 国家与时区还有键盘布局，国家填好之后时区会自动跟着变，键盘布局一般不需要改：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211433.webp)

再接下来就是设置 root 的密码以及邮箱：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211450.webp)

再是到这里设置 网络，<strong>网卡要选择 开头为 eno 的</strong>，根据你网线插在哪个口选择对应的，千万不要选错，不然进不了管理后台(坑了我很久，我还以为是我设置错了)，再接着设置 主机名，ip，掩码，网关与dns等：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_214444.webp)

最后是确认界面，确认无误之后，就点击 **Install** 进行安装：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_214541.webp)

安装中：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211709.webp)

最后跳转到这个界面的时候就安装成功了，点击右下角 **Reboot** 重启，正常启动：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200420_211915.webp)

最后就是进入 Web UI 界面进行管理了，要注意的是 地址是：**https://server-ip:8006**，是 **https** 而不是 http：

![](/images/articles/microserver-gen10-plus-proxmox-ve/20200422_223141.webp)