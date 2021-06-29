---
title: Proxmox VE 在 Ubuntu 中 N卡直通
date: 2020-04-22 10:19:09
toc: true
categories: 
 - Microserver Gen 10 Plus
tags:
 - Proxmox VE
---

由于 Microserver Gen10 Plus 中 CPU 为 E-2224 这款没有核显，而我有 HTPC 的需求，所以我在唯一的一个 PCI-E 接口上了一块 GT-1030，然后由于向 ESXI 中虚拟机传输文件的速度实在是慢得不行，我千兆的内网，传输文件时只能跑出 100-200mbps，不能忍，所以我改安装 Proxmox VE 了。

<!-- more -->

## 1.Gobal Setting

首先，根据 [Proxmox VE 官方文档](https://pve.proxmox.com/wiki/PCI(e)_Passthrough) 给出的 PCI-E 直通的前提条件，修改 Proxmox 主机中的 `/etc/default/grub` 来启用 `IOMMU` ：

```bash
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'

GRUB_DEFAULT=0
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="Proxmox Virtual Environment"
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX=""

# Disable os-prober, it might add menu entries for each guest
GRUB_DISABLE_OS_PROBER=true

# Uncomment to enable BadRAM filtering, modify to suit your needs
# This works with Linux (no patch required) and with any kernel that obtains
# the memory map information from GRUB (GNU Mach, kernel of FreeBSD ...)
#GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

# Uncomment to disable graphical terminal (grub-pc only)
#GRUB_TERMINAL=console

# The resolution used on graphical terminal
# note that you can use only modes which your graphic card supports via VBE
# you can see them in real GRUB with the command `vbeinfo'
#GRUB_GFXMODE=640x480

# Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
#GRUB_DISABLE_LINUX_UUID=true

# Disable generation of recovery mode menu entries
GRUB_DISABLE_RECOVERY="true"

# Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"
```

将第 9 行的：

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
```

如果你是 Intel CPU 改为：

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```

如果你是 AMD YES 的话，改为：

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"
```

保存之后执行以下命令来进行更新：

```bash
update-grub
```

再将下面的添加到 `/etc/modules` 中：

```bash
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```

执行以下命令来刷新 `initramfs` ：

```bash
update-initramfs -u -k all
```

重启 Proxmox 主机，再执行以下命令来验证是否成功开启 `IOMMU` ：

```bash
dmesg | grep -e DMAR -e IOMMU -e AMD-Vi
```

返回类似如下的内容：

```
[    0.025301] ACPI: DMAR 0x000000007BFE6000 0000B0 (v01 HPE    Server   00000001 1590 00000001)
[    0.061429] DMAR: IOMMU enabled
[    0.116477] DMAR: Host address width 39
[    0.116478] DMAR: DRHD base: 0x000000fed91000 flags: 0x1
[    0.116481] DMAR: dmar0: reg_base_addr fed91000 ver 1:0 cap d2008c40660462 ecap f050da
[    0.116482] DMAR: RMRR base: 0x000000799d7000 end: 0x000000799f6fff
[    0.116482] DMAR: RMRR base: 0x000000799c6000 end: 0x000000799c6fff
[    0.116483] DMAR-IR: IOAPIC id 2 under DRHD base  0xfed91000 IOMMU 0
[    0.116484] DMAR-IR: HPET id 0 under DRHD base 0xfed91000
[    0.116484] DMAR-IR: Queued invalidation will be enabled to support x2apic and Intr-remapping.
[    0.118014] DMAR-IR: Enabled IRQ remapping in x2apic mode
[    0.593568] DMAR: No ATSR found
[    0.593593] DMAR: dmar0: Using Queued invalidation
[    0.595251] DMAR: Intel(R) Virtualization Technology for Directed I/O
```

其中 第 2 行，`IOMMU enable` 则证明启用成功

## 2.GPU Passthrough

通过在 Proxmox 主机上执行如下命令来获取 GPU 的 ID：

```bash
lspci -nn
```

输出如下内容：

```
00:00.0 Host bridge [0600]: Intel Corporation Device [8086:3e33] (rev 07)
00:01.0 PCI bridge [0604]: Intel Corporation Skylake PCIe Controller (x16) [8086:1901] (rev 07)
00:12.0 Signal processing controller [1180]: Intel Corporation Cannon Lake PCH Thermal Controller [8086:a379] (rev 10)
00:14.0 USB controller [0c03]: Intel Corporation Cannon Lake PCH USB 3.1 xHCI Host Controller [8086:a36d] (rev 10)
00:14.2 RAM memory [0500]: Intel Corporation Cannon Lake PCH Shared SRAM [8086:a36f] (rev 10)
00:16.0 Communication controller [0780]: Intel Corporation Cannon Lake PCH HECI Controller [8086:a360] (rev 10)
00:16.4 Communication controller [0780]: Intel Corporation Device [8086:a364] (rev 10)
00:17.0 SATA controller [0106]: Intel Corporation Cannon Lake PCH SATA AHCI Controller [8086:a352] (rev 10)
00:1b.0 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a32c] (rev f0)
00:1c.0 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a338] (rev f0)
00:1d.0 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a330] (rev f0)
00:1d.1 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a331] (rev f0)
00:1d.2 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a332] (rev f0)
00:1d.3 PCI bridge [0604]: Intel Corporation Cannon Lake PCH PCI Express Root Port [8086:a333] (rev f0)
00:1f.0 ISA bridge [0601]: Intel Corporation Device [8086:a30a] (rev 10)
00:1f.5 Serial bus controller [0c80]: Intel Corporation Cannon Lake PCH SPI Controller [8086:a324] (rev 10)
01:00.0 System peripheral [0880]: Hewlett-Packard Company Integrated Lights-Out Standard Slave Instrumentation &amp; System Support [103c:3306] (rev 07)
01:00.1 VGA compatible controller [0300]: Matrox Electronics Systems Ltd. MGA G200eH3 [102b:0538] (rev 02)
01:00.2 System peripheral [0880]: Hewlett-Packard Company Integrated Lights-Out Standard Management Processor Support and Messaging [103c:3307] (rev 07)
01:00.4 USB controller [0c03]: Hewlett-Packard Company iLO5 Virtual USB Controller [103c:22f6]
02:00.0 Ethernet controller [0200]: Intel Corporation I350 Gigabit Network Connection [8086:1521] (rev 01)
02:00.1 Ethernet controller [0200]: Intel Corporation I350 Gigabit Network Connection [8086:1521] (rev 01)
02:00.2 Ethernet controller [0200]: Intel Corporation I350 Gigabit Network Connection [8086:1521] (rev 01)
02:00.3 Ethernet controller [0200]: Intel Corporation I350 Gigabit Network Connection [8086:1521] (rev 01)
07:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP108 [10de:1d01] (rev a1)
07:00.1 Audio device [0403]: NVIDIA Corporation GP108 High Definition Audio Controller [10de:0fb8] (rev a1)
```

最后两行是我的显卡，记下他们的 ID：

```bash
07:00.0 [10de:1d01]
07:00.1 [10de:0fb8]
```

再接着，在 `/etc/modprobe.d` 中创建 `vfio.conf` 文件，将以下内容写到里面：

```
options vfio-pci ids=10de:1d01,10de:0fb8 disable-vga=1
```

其中 ids 后面的内容是我前面记下的 GPU 的 ID，用 `,` 号分隔

再在同目录下新建一个 `blacklist.conf` 写入如下内容来保证 GPU 可以自由绑定而不会因为被 Proxmox 主机占用而出错，其中 `radeon` 是 A卡的驱动，`nouveau` 是自带的N卡驱动，就在这里连 A卡驱动一起给 ban 了：

```
blacklist radeon
blacklist nouveau
blacklist nvidia
```

再执行以下命令来刷新 `initramfs`：

```bash
update-initramfs -u -k all
```

执行之后再重启 Proxmox 主机，重启完成之后，再执行如下命令验证：

```bash
lspci -nnk
```

输出类似如下：

```
...
07:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP108 [10de:1d01] (rev a1)
        Subsystem: Gigabyte Technology Co., Ltd GP108 [GeForce GT 1030] [1458:375c]
        Kernel driver in use: vfio-pci
        Kernel modules: nvidiafb, nouveau
07:00.1 Audio device [0403]: NVIDIA Corporation GP108 High Definition Audio Controller [10de:0fb8] (rev a1)
        Subsystem: Gigabyte Technology Co., Ltd GP108 High Definition Audio Controller [1458:375c]
        Kernel driver in use: vfio-pci
        Kernel modules: snd_hda_intel
```

其中第 4 行与 第 8 行显示为：

```
Kernel driver in use: vfio-pci
```

则证明显卡直通成功了

## 3.配置虚拟机

这里以 **Ubuntu Server 18.04 LTS** 为例：

![创建](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111152.webp)

![设置镜像](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111236.webp)

这里要注意的是 BIOS 要设置为 **OVMF(UEFI)** 至于是否要添加 EFI 磁盘，有什么影响我还不确定，但是我是添加了，还有就是 机器要设置为 **q35**

![系统设置](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111405.webp)

![硬盘设置](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111724.webp)

![设置CPU](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111759.webp)

![设置内存](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111834.webp)

![设置网络](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_111940.webp)

![总设置](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_112004.webp)

在创建完之后，先不要急着启动，找到侧边栏的 **硬件** 选项：

![添加 PCI-E 硬件](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_141610.webp)

再选中 **显示** 这一行，点击编辑，修改为 **SPICE** ：

![编辑显存为SPICE](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_142041.webp)

最后修改完的设置应该为：

![总配置](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_142903.webp)

然后再去启动虚拟机。

## 4.Ubuntu 安装显卡驱动

通过 VNC 安装完系统之后，执行以下命令来安装系统更新：

```bash
apt update && apt upgrade -y
```

再安装 **ubuntu-drivers-common** 来安装驱动：

```bash
apt install ubuntu-drivers-common
```

ubuntu-drivers 安装完后再执行以下命令来自动安装驱动：

```bash
ubuntu-drivers autoinstall
```

这个驱动安装完后执行如下命令来验证：

```bash
nvidia-smi
```

如果报错，类似：

**NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver**

则再执行如下命令安装 cuda toolkit：

```bash
apt install nvidia-cuda-toolkit
```

安装完成后再执行：

```bash
nvidia-smi
```

输出：

![](/images/articles/proxmox-ve-ubuntu-nvidia-passthrough/20200731_143853.webp)

则成功直通